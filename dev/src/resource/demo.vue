<template>
  <div data-role="demo">
    <h1>Vue Tinymce</h1>
    
    <div>
      <select v-model="locale">
        <option value="zh_TW">Chinese</option>
        <option value="en_CA">English</option>
        <option value="ja">Japenese</option>
      </select>
    </div>

    <div class="result" v-html="data"></div>

    <div class="tinymce-wrap">
      <Tinymce
        :language="language"
        :language_url="language_url"
        :photoUploadRequest="photoUploadRequest"
        v-model="data"/>
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
    }
  },
  computed: {
    language: function() {
      return this.locale
    },
    language_url: function() {
      return `/static/langs/${this.locale}.js`
    },
    photoUploadRequest: function(url, data, onProgress = null) {
      return function(url, data, onProgress) {
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