<template>
  <div>
    <input type="file" v-if="hasFileBrowser">
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
      this.setupFileBrowser()
    },
    setupFileBrowser: function () {
      if(!this.hasFileBrowser) return

      this.fileBrowser.change(event => {
        if(event.target.files.length == 0) return
        this.uploadPhoto(event.target.files[0])
      })
    },
    onProgress: function(progress) {
      console.warn('progress', progress)
    },
    uploadPhoto: async function(file) {
      try {
        const result = await this.photoUploadRequest(file, this.onProgress)
        this.$emit('uploadSuccess', {
          result: result,
        })

        this.appendImage(result)
      } catch (error) {
        this.$emit('uploadFail', {
          result: result,
        })
      }
    },
    appendImage: function (result) {
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

<style lang="sass" type="text/sass" scoped>
input[type="file"]
  opacity: 0
</style>