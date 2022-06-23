<template>
  <div class="search-result">
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      :error.sync="error"
      error-text="加载失败，请点击重试"
      @load="onLoad"
    >
      <van-cell v-for="(item, index) in list" :key="index" :title="item.title" />
    </van-list>
  </div>
</template>

<script>
import { getSearchResult } from '@/api/search'
export default {
  name: 'search-result',
  data() {
    return {
      list: [],
      loading: false,
      finished: false,
      page: 1,
      per_page: 15,
      error: false
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
  watch: {},
  methods: {
    async onLoad () {
      try {
        // 1请求获取数据
        const { data } = await getSearchResult({
          page: this.page,
          per_page: this.per_page,
          q: this.searchText
        })
        // 模拟随机失败的情况
        // if (Math.random() > 0.5) {
        //   JSON.parse('dsnajndjsa')
        // }
        // console.log(data)
        // 2将数据添加到数组列表中
        const { results } = data.data
        this.list.push(...results)
        // 3将本次加载中的loanding关闭
        this.loading = false
        // 4判断是否还有数据
        if (results.length) {
          this.page++
        } else {
          this.finished = true
        }
        // 有，则更新获取下一个数据的页码
        // 没有，则将加载状态 finished设置为结束
      } catch (err) {
        this.error = true
        this.loading = false
        this.$toast('数据获取失败')
      }
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less"></style>
