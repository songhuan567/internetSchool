<template>
  <div class="components-container">
    <!-- <aside>
      <a target="_blank" class="link-type" href="https://panjiachen.github.io/vue-element-admin-site/feature/component/rich-editor.html"></a>
    </aside> -->
    <div>
      <tinymce v-model="content" :height="300" />
    </div>
    <!-- <div class="editor-content" v-html="content" /> -->
  </div>
</template>

<script>
import Tinymce from '@/components/Tinymce'

export default {
  name: 'TinymceDemo',
  components: { Tinymce },
  props: {
    content:{
      type: String,
    }
  },
  data() {
   
  }
}
</script>

<style scoped>
.editor-content{
  margin-top: 20px;
}
</style>

