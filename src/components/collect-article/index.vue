<template>
  <van-icon
    :class="{ collected: value }"
    :name="value ? 'star' : 'star-o'"
    :loading="loading"
    @click="onCollect"
  />
</template>

<script>
import { addCollect, deleteCollect } from '@/api/article'
export default {
  name: 'CollectArticle',
  data() {
    return {
      loading: false
    }
  },
  components: {},
  props: {
    value: {
      type: Boolean,
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
    async onCollect() {
      this.loading = true
      try {
        if (this.value) {
          await deleteCollect(this.articleId)
        } else {
          await addCollect(this.articleId)
        }
        //   更新视图
        this.$emit('input', !this.value)
        this.$toast.success(this.value ? '取消收藏' : '收藏成功')
      } catch (err) {
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
.collected {
  color: #ffa400;
}
</style>
