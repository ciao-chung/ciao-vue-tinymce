<template>
  <div ciao-vue-tinymce="photo-upload">
    <input type="file" v-if="hasFileBrowser">

    <div class="progress-wrap">
      <div class="progress" v-if="progressPercentage > 0">
        <div class="progress-bar progress-bar-success" :style="'width: '+progressPercentage+'%'"></div>
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
    progress: {
      type: Boolean,
    },
  },
  data: function () {
    return {
      editor: null,
      progressPercentage: 0,
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
        this.uploadPhoto(event.target.files[0])
      })
    },
    onProgress: function(progress) {
      if(!this.progress) return
      this.progressPercentage = Math.floor(progress.loaded/progress.total*100)
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
          result: result,
        })
        this.resetProgress()
      }
    },
    resetProgress: function () {
      this.progressPercentage = 0
    },
    insertImageToEditor: function (result) {
      let imageTag = `<img src="${result.url}" />`
      if(this.photoUploadTag instanceof Function) imageTag = this.photoUploadTag(result)
      this.editor.insertContent(imageTag)
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
  .progress-wrap
    height: 5px
    .progress
      padding: 0
      margin: 0
</style>