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
        v-model="data"/>
    </div>
  </div>
</template>

<script>
import Tinymce from 'src/../index.js'
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