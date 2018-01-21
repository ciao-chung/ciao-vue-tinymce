<template>
  <div>
    <h1>Demo</h1>
    
    <div class="tinymce-wrap">
      <Tinymce/>
    </div>
  </div>
</template>

<script>
import Tinymce from 'components/Tinymce.vue'
export default {
  components: {
    Tinymce,
  },
}
</script>

<style lang="sass" type="sass/text" scoped>
.tinymce-wrap
  width: 80%
  padding: 10px
</style>