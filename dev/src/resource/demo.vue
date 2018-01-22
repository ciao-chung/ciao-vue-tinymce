<template>
  <div data-role="demo">
    <h1>Ciao Vue Tinymce</h1>

    <div class="row">
      <div class="col-md-8">
        <div class="input-group">
          <span class="input-group-addon">Language</span>

          <select v-model="locale" class="form-control">
            <option value="zh_TW">Chinese</option>
            <option value="en_CA">English</option>
            <option value="ja">Japenese</option>
          </select>
        </div>

        <div class="tinymce-wrap">
          <Tinymce
            @uploadSuccess="uploadSuccess"
            @uploadFail="uploadFail"
            :tools="tools"
            :photoUploadTag="photoUploadTag"
            :language="language"
            :language_url="language_url"
            :photoUploadRequest="photoUploadRequest"
            v-model="data"/>
        </div>
      </div>

      <div class="col-md-4">
        <div class="result" v-html="data"></div>
      </div>
    </div>

  </div>
</template>

<script>
import Tinymce from 'src/../../index.js'
export default {
  data: function() {
    return {
      data: null,
      locale: 'zh_TW',
      tools: [
        {
          text: 'A',
          icon: 'image',
          onclick: (editor) => console.warn('onclick', editor)
        }
      ]
    }
  },
  methods: {
    uploadSuccess: function (data) {
      console.log('uploadSuccess', data)
    },
    uploadFail: function (error) {
      console.warn('uploadFail', error)
    },
    photoUploadTag: function (result) {
      return `<img src="${result.url}" class="img-responsive" />`
    }
  },
  computed: {
    language: function() {
      return this.locale
    },
    language_url: function() {
      return `/static/langs/${this.locale}.js`
    },
    photoUploadRequest: function(file, onProgress = null) {
      return function(file, onProgress) {
        return new Promise(resolve => {
          if(onProgress)
            for(let i=1; i<=5; i++) {
              ((i) => {
                setTimeout(() => {
                  onProgress({ loaded: 20 * i, total: 100 })
                }, 200)
              })(i)
            }

          setTimeout(() => {
            resolve({
              url: 'https://vuejs.org/images/logo.png',
            })
          }, 1000)
        })
      }
    },
  },
  components: {
    Tinymce,
  },
}
</script>

<style lang="sass" type="sass/text" scoped>
div[data-role="demo"]
  &>div
    width: 80%
    padding: 20px
</style>