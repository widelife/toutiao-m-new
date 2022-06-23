<template>
  <van-button
    v-if="isFld"
    class="follow-btn"
    round
    size="small"
    :loading="loading"
    @click="onFollow"
    >已关注</van-button
  >
  <van-button
    v-else
    :loading="loading"
    @click="onFollow"
    class="follow-btn"
    type="info"
    color="#3296fa"
    round
    size="small"
    icon="plus"
    >关注</van-button
  >
</template>

<script>
import { deleteFollow, addFollow } from '@/api/user'
export default {
  name: 'FollowUser',
  data() {
    return {
      loading: false
    }
  },
  //   自定义v-model的数据名称
  model: {
    prop: 'isFld',
    event: 'updateFld'
  },
  components: {},
  props: {
    isFld: {
      type: Boolean,
      require: true
    },
    userId: {
      type: [Number, String, Object],
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    async onFollow() {
      this.loading = true // 展示关注按钮的loading
      try {
        if (this.isFld) {
          await deleteFollow(this.userId)
          //   this.isFld = false
        } else {
          await addFollow(this.userId)
          //   this.isFld = true
        }
        // 更新视图状态 老师说不能再子组件改 我感觉可以因为他又没有改变父组件中data中article再服务器中的数据
        // this.isFld = !this.isFld
        this.$emit('updateFld', !this.isFld)
      } catch (err) {
        let message = '操作失败'
        if (err.response && err.response.status === 400) {
          message = '你不能关注你自己'
        }
        this.$toast(message)
      }
      this.loading = false
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less"></style>
