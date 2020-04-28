<template>
  <div class="name11">
    <h1>测试效果2</h1>
    <tinymce ref="editor" v-model="msg" :disabled="disabled" />
    <button @click="clear">测试输出内容</button>
    <button @click="disabled = !disabled">禁用/启用</button>
  </div>
</template>
<script>
import tinymce from "@/components/tiny/tiny2.vue";
export default {
  name: "name11",
  components: {
    tinymce
  },
  data() {
    return {
      msg: "Welcome to Use Tinymce Editor",
      disabled: false
    };
  },
  methods: {
    // 鼠标单击的事件
    onClick(e, editor) {
      console.log("Element clicked");
      console.log(e);
      console.log(editor);
    },
    // 清空内容
    clear() {
      this.$refs.editor.clear();
    }
  }
};
</script>
