<template>
  <div data-role="demo">
    <h1>Vue Tinymce</h1>
    
    <div class="result" v-html="data"></div>

    <div class="tinymce-wrap">
      <Tinymce v-model="data"/>
    </div>
  </div>
</template>

<script>
import Tinymce from 'src/../index.js'
export default {
  data: function() {
    return {
      data: null,
    }
  },
  components: {
    Tinymce,
  },
}
</script>

<style lang="sass" type="sass/text" scoped>
div[data-role="demo"]
  &>div
    width: 100%
    padding: 10px
</style>