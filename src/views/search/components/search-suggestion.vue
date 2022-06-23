<template>
  <div class="search-suggestion">
    <van-cell
     icon="search"
     v-for="(text, index) in suggestions" :key="index"
     @click="$emit('search',text)"
     >
      <div slot="title" v-html="highlight(text)">123</div>
    </van-cell>
    <!-- 双花括号绑定会直接输出存文本内容 -->
    <!-- <div>{{ htmlStr }}</div> -->
    <!-- 使用v-html指令可以绑定渲染带有htnl标签的字符串 -->
    <!-- <div v-html="htmlStr"></div> -->
  </div>
</template>

<script>
import { getSearchSuggestion } from '@/api/search'
import { debounce } from 'lodash'
export default {
  name: 'search-suggestion',
  data() {
    return {
      suggestions: [],
      htmlStr: 'hello <span style="color: red">world</span>'
    }
  },
  components: {},
  props: {
    searchText: {
      type: String,
      require: true
    }
  },
  computed: {},
  watch: {
    searchText: {
      // 当searchText发生改变的时候会调用handler函数
      // 注意handler函数名是固定的
      // debounce防抖函数 参数1 一个函数 参数2 延时函数 返回值 防抖之后的函数
      handler: debounce(function(value) {
        this.loadSearchSuggestion(value)
      }, 500),
      // handler(value) {
      //   this.loadSearchSuggestion(value)
      // },
      immediate: true // 该回调将会在侦听开始之后被立即调用
    }
  },
  methods: {
    async loadSearchSuggestion(q) {
      const { data } = await getSearchSuggestion(q)
      this.suggestions = data.data.options
    },
    // async loadSearchSuggestion (q) {
    //   try {
    //     const { data } = await getSearchSuggestion(q)
    //     this.suggestions = data.data.options
    //   } catch (err) {
    //     this.$toast('获取数据失败')
    //   }
    // }
    highlight(text) {
      const highlightStr = `<span class="active">${this.searchText}</span>`
      // 正则表达式中间的内容会当作匹配的字符来使用而不会当作数据变量
      // 如果需要根据数据的变量动态创建正贼表达式，贼手动 new RegExp
      // RegExp 正则表达式的构造函数g全局i不区分大小写
      const reg = new RegExp(this.searchText, 'gi')
      if (text) {
        return text.replace(reg, highlightStr)
      }
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less">
.search-suggestion {
  /deep/ span.active {
    color: #2d93fa;
  }
}
</style>
