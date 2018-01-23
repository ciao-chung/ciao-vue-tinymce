<template>
  <div ciao-vue-tinymce="photo-upload">
    <input type="file" v-if="hasFileBrowser">

    <div class="progress-wrap">
      <div class="progress" v-if="progress > 0">
        <div class="progress-bar progress-bar-success" :style="'width: '+progress+'%'"></div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from 'jquery'
export default {
  props: {
    hasFileBrowser: {
      type: Boolean,
    },
    photoUploadRequest: {
      type: Function,
      default: null,
    },
    photoUploadTag: {
      type: Function,
      default: null,
    },
  },
  data: function () {
    return {
      editor: null,
      progress: 0,
    }
  },
  mounted: function () {
    this.init()
  },
  methods: {
    addTool: function (editor) {
      this.editor = editor

      editor.addButton('photoUploadButton', {
        icon: 'upload',
        onclick: () => this.fileBrowser.click()
      })
    },
    init: function () {
      if(!this.hasFileBrowser) return

      this.fileBrowser.change(event => {
        if(event.target.files.length == 0) return
        const file = new FormData()
        file.append('file', event.target.files[0])
        this.uploadPhoto(file)
      })
    },
    onProgress: function(progress) {
      this.progress = Math.floor(progress.loaded/progress.total*100)
    },
    uploadPhoto: async function(file) {
      try {
        const result = await this.photoUploadRequest(file, this.onProgress)
        this.$emit('uploadSuccess', {
          result: result,
        })

        this.insertImageToEditor(result)
        this.resetProgress()
      } catch (error) {
        this.$emit('uploadFail', {
          result: error,
        })
        this.resetProgress()
      }
    },
    resetProgress: function () {
      this.fileBrowser.val('')
      setTimeout(() => this.progress = 0, 1000)
    },
    insertImageToEditor: function (result) {
      let imageTag = `<img src="${result.url}" />`
      if(this.photoUploadTag instanceof Function) imageTag = this.photoUploadTag(result)
      this.editor.insertContent(imageTag)
      this.$emit('update')
    },
  },
  computed: {
    fileBrowser: function () {
      return $(this.$el).find('input[type="file"]')
    },
  },
}
</script>

<style src="bootstrap/dist/css/bootstrap.min.css"></style>
<style lang="sass" type="text/sass" scoped>
div[ciao-vue-tinymce="photo-upload"]
  input[type="file"]
    opacity: 0
    width: 0
    height: 0
    pointer-events: none
  .progress-wrap
    height: 5px
    .progress
      padding: 0
      margin: 0
</style>