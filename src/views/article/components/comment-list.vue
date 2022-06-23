<template>
  <van-list
    v-model="loading"
    :finished="finished"
    finished-text="没有更多了"
    :error="error"
    error-text="加载失败点击重试"
    @load="onLoad"
    :immediate-check="false"
  >
    <CommentItem
      v-for="(item, index) in list"
      :key="index"
      :comment="item"
      @reply-click="$emit('reply-click', $event)"
    />
  </van-list>
</template>

<script>
import { getComments } from '@/api/comment'
import CommentItem from './comment-item.vue'
export default {
  name: 'CommentList',
  data() {
    return {
      // list: [],
      loading: false,
      finished: false,
      offset: null, // 获取下一页的标记
      limit: 10,
      error: false
    }
  },
  components: {
    CommentItem
  },
  props: {
    source: {
      type: [Number, String, Object],
      require: true
    },
    list: {
      type: Array,
      default: () => []
    },
    type: {
      type: String,
      // 自定义prop数据验证
      validator(value) {
        return ['a', 'c'].includes(value)
      },
      default: 'a'
    }
  },
  computed: {},
  watch: {},
  methods: {
    async onLoad() {
      try {
        // 1请求获取数据
        const { data } = await getComments({
          type: this.type, // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
          source: this.source.toString(), // 源id，文章id或评论id
          offset: this.offset, // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
          limit: this.limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
        })
        // console.log(11)
        // 2将数据添加到列表中
        // console.log(data)
        const { results } = data.data
        this.list.push(...results)
        // 将评论总数传递到父组件中
        this.$emit('onload-success', data.data)
        // 3将loading设置为false
        this.loading = false
        // 4判断是否还有数据
        if (results.length) {
          this.offset = data.data.last_id
        } else {
          this.finished = true
        }
        // 有就更新获取下一页数据码
        // 没有就将finish设置为结束
      } catch (err) {
        this.error = true
        this.loading = false
      }
    }
  },
  created() {
    // 当你手动初始onload函数的华,他不会自动初始loading
    this.loading = true
    this.onLoad()
  },
  mounted() {}
}
</script>

<style scoped lang="less"></style>
