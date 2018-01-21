<template>
  <div ciao-vue-tinymce>
    tinymce
    <textarea :tinymce="uuid"></textarea>
  </div>
</template>

<script>
import 'tinymce/tinymce'
import 'tinymce/themes/modern/theme'
import 'tinymce/plugins/paste/plugin'
import 'tinymce/plugins/link/plugin'
import 'tinymce/plugins/autoresize/plugin'
import 'tinymce/plugins/image/plugin'
import 'tinymce/plugins/media/plugin'
import 'tinymce/plugins/table/plugin'
import 'tinymce/plugins/code/plugin'
import 'tinymce/plugins/advlist/plugin'
import 'tinymce/plugins/autolink/plugin'
import 'tinymce/plugins/lists/plugin'
import 'tinymce/plugins/charmap/plugin'
import 'tinymce/plugins/print/plugin'
import 'tinymce/plugins/preview/plugin'
import 'tinymce/plugins/anchor/plugin'
import 'tinymce/plugins/searchreplace/plugin'
import 'tinymce/plugins/visualblocks/plugin'
import 'tinymce/plugins/fullscreen/plugin'
import 'tinymce/plugins/insertdatetime/plugin'
import 'tinymce/plugins/contextmenu/plugin'
import 'tinymce/plugins/textcolor/plugin'
import uuidV4 from 'uuid/v4'
export default {
  props: {
    language: {
      type: String,
      default: '',
    },
    language_url: {
      type: String,
      default: '',
    },
    photoUploadRequest: {
      type: Promise,
      default: null,
    },
    value: {
      type: String,
      default: '',
    },
  },
  mounted: function() {
    this.init()
  },
  methods: {
    destroy: function() {
      tinymce.remove(this.editorSelector)
    },
    setup: function() {
      tinymce.init({
        selector: this.editorSelector,
        skin: false,
        min_height: 300,
        language: this.language,
        language_url: this.language_url,
        destroy: true,
        plugins: [
          'advlist autolink lists link image charmap print preview anchor',
          'searchreplace visualblocks code fullscreen',
          'insertdatetime media table contextmenu paste code table',
          'textcolor',
        ],
        toolbar: 'code | undo redo | insert | styleselect | forecolor backcolor | bold italic | alignleft aligncenter alignright alignjustify | bullist numlist outdent indent | table | link image | fullscreen',
        menubar: false,
        setup: editor => {
          editor.on('blur', () => this.$emit('input', editor.getContent()))
        }
      })
    },
    init: function() {
      this.destroy()
      this.setup()
    },
  },
  computed: {
    uuid: function() {
      return uuidV4()
    },
    editorSelector: function() {
      return `textarea[tinymce=${this.uuid}]`
    },
  },
  watch: {
    value: function() {
      this.init()
    },
    language: function() {
      this.init()
    },
  },
}
</script>

<style src="tinymce/skins/lightgray/skin.min.css"></style>
<style src="tinymce/skins/lightgray/content.min.css"></style>
<style lang="sass" type="text/sass" scoped>
div[ciao-vue-tinymce]
  padding: 0
</style>