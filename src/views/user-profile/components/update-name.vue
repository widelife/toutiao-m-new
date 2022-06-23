<template>
  <div class="update-name">
    <van-nav-bar
      title="设置昵称"
      left-text="取消"
      right-text="完成"
      @click-left="$emit('close')"
      @click-right="onUpdateUserFile"
    ></van-nav-bar>
    <!-- 输入框 -->
    <div class="field-wrap">
      <van-field
        v-model.trim="message"
        rows="2"
        autosize
        type="textarea"
        maxlength="7"
        placeholder="请输入昵称"
        show-word-limit
      />
    </div>
  </div>
</template>

<script>
import { updateUserProfile } from '@/api/user'
export default {
  name: 'UpdateName',
  data() {
    return {
      message: this.value
    }
  },
  components: {},
  props: {
    value: {
      type: String,
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    async onUpdateUserFile() {
      this.$toast.loading({
        message: '保存中...',
        forbidClick: true, // 禁止背景点击
        duration: 0 // 持续展示
      })
      try {
        // const message = this.message
        if (!this.message.length) {
          this.$toast('昵称不能为空')
          return
        } else {
          await updateUserProfile({
            name: this.message
          })
          //   console.log(data)
          //   更新视图
          this.$emit('input', this.message)
          //   关闭弹层
          this.$emit('close')
          //   提示成功
          this.$toast.success('更新成功')
        }
      } catch (err) {
        this.$toast('更新失败')
      }
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less">
.field-wrap {
  padding: 20px;
}
</style>
