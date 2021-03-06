<template>
  <div class="editor-wrapper">
    <div :id="editorId"></div>
  </div>
</template>

<script>
import Editor from "wangeditor";
import "wangeditor/release/wangEditor.min.css";
import { oneOf } from "@/lib/util";
export default {
  name: "Editor",
  props: {
    // 缓存Html内容
    value: {
      type: String,
      default: "",
    },
    /**
     * 绑定的值的类型, enum: ['html', 'text']
     */
    valueType: {
      type: String,
      default: "html",
      validator: (val) => {
        return oneOf(val, ["html", "text"]);
      },
    },
    /**
     * @description 设置change事件触发时间间隔
     */
    changeInterval: {
      type: Number,
      default: 200,
    },
    /**
     * @description 是否开启本地存储
     */
    cache: {
      type: Boolean,
      default: true,
    },
    showFlag: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    // 唯一id
    editorId() {
      return `editor${this._uid}`;
    },
  },
  mounted() {
    console.log(`#${this.editorId}`);
    this.editor = new Editor(`#${this.editorId}`);
    this.editor.i18next = window.i18next;
    this.editor.customConfig.onchange = (html) => {
      let text = this.editor.txt.text();
      if (this.cache) localStorage.editorCache = html;
      this.$emit("input", this.valueType === "html" ? html : text);
      this.$emit("on-change", html, text);
    };
    this.editor.customConfig.onchangeTimeout = this.changeInterval;
    // create这个方法一定要在所有配置项之后调用
    this.editor.create();
  },
  watch: {
    showFlag() {
      // 如果本地有存储加载本地存储内容
      let html = this.value || localStorage.editorCache;
      console.log(html);
      if (html) {
        this.editor.txt.html(html)
      }else {
        this.editor.txt.html()
      } ;
    },
  },
  activated() {},
  methods: {
    setHtml(val) {
      this.editor.txt.html(val);
    },
  },
};
</script>

<style lang="less">
.editor-wrapper * {
  z-index: 100 !important;
}
</style>
