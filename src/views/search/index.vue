<template>
  <div class="search-container">
    <!-- 搜索页面 -->
    <!-- Tips: 在 van-search 外层增加 form 标签，且 action 不为空，即可在 iOS 输入法中显示搜索按钮。 -->
    <form class="search-form" action="/">
      <van-search
        v-model="searchText"
        show-action
        placeholder="请输入搜索关键词"
        background="#2892ff"
        @search="onSearch"
        @cancel="onCancel"
        @focus="isReasultShow = false"
      />
    </form>
    <!-- 搜索结果 -->
    <search-result v-if="isReasultShow"
     :search-text="searchText"
     />
    <!-- 搜索结果 -->
    <!-- 联想建议 -->
    <search-suggestion
     v-else-if="searchText"
     :search-text="searchText"
     @search="onSearch"
     />
    <!-- 联想建议 -->
    <!-- 搜索历史记录 -->
    <search-history
    v-else
    :seachHistories="searchHistories"
    @quanbushanchu="searchHistories = []"
    @search="onSearch"
    />
    <!-- 搜索历史记录 -->
  </div>
</template>

<script>
import SearchHistory from './components/search-history'
import SearchSuggestion from './components/search-suggestion'
import SearchResult from './components/search-result'
import { setItem, getItem } from '@/utils/storage'

export default {
  name: 'SearchIndex',
  data() {
    return {
      searchText: '',
      isReasultShow: false, // 控制搜索结果的展示
      searchHistories: getItem('TOUTIAO_SEARCH_HISTORIES') || []
    }
  },
  components: {
    SearchHistory,
    SearchSuggestion,
    SearchResult
  },
  props: {},
  computed: {},
  watch: {
    searchHistories (val) {
      setItem('TOUTIAO_SEARCH_HISTORIES', val)
    }
  },
  methods: {
    onSearch(val) {
      // 更新文本框内容
      this.searchText = val
      // 存储搜索历史记录
      // 不要有重复的历史纪录最新的排在前面
      const index = this.searchHistories.indexOf(val)
      if (index !== -1) {
        this.searchHistories.splice(index, 1)
      }
      this.searchHistories.unshift(val)
      // 渲染搜索结果
      this.isReasultShow = true
    },
    onCancel() {
      this.$router.back()
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less">
.search-container {
  padding-top: 108px;
    .van-search__action {
        color: #fff;
    }
    .search-form{
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      z-index: 1;
    }
}
</style>
