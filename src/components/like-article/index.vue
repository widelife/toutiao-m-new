<template>
  <van-icon
    :class="{ liked: value === 1 }"
    :name="value === 1 ? 'good-job' : 'good-job-o'"
    :loading="loading"
    @click="onliked"
  />
</template>

<script>
import { addLike, deleteLike } from '@/api/article'
export default {
  name: 'LikeArticle',
  data() {
    return {
      loading: false
    }
  },
  components: {},
  props: {
    value: {
      type: Number,
      require: true
    },
    articleId: {
      type: [Number, String, Object],
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    async onliked() {
      this.loading = true
      try {
        let status = -1
        if (this.value === 1) {
          await deleteLike(this.articleId)
        } else {
          await addLike(this.articleId)
          status = 1
        }
        //   更新视图
        this.$emit('input', status)
        this.$toast.success(status === 1 ? '点赞成功' : '取消点赞')
      } catch (err) {
        // console.log(err)
        this.$toast.fail('操作失败')
      }
      this.loading = true
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less">
.liked {
  color: #ef715e;
}
</style>
