<template>
  <div class="update-gender">
    <van-picker
      title="标题"
      show-toolbar
      :columns="columns"
      @cancel="$emit('close')"
      @confirm="onConfirm"
      :default-index="value"
      @change="onPickChange"
    />
  </div>
</template>

<script>
import { updateUserProfile } from '@/api/user'
export default {
  name: 'UpdateGender',
  data() {
    return {
      columns: ['难', '钕'],
      locaGender: this.value
    }
  },
  components: {},
  props: {
    value: {
      type: Number,
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    async onConfirm() {
      this.$toast.loading({
        message: '保存中...',
        forbidClick: true, // 禁止背景点击
        duration: 0 // 持续展示
      })
      try {
        // const message = this.message
        await updateUserProfile({
          gender: this.locaGender
        })
        //   console.log(data)
        //   更新视图
        this.$emit('input', this.locaGender)
        //   关闭弹层
        this.$emit('close')
        //   提示成功
        this.$toast.success('更新成功')
      } catch (err) {
        this.$toast('更新失败')
      }
    },
    onPickChange(picker, value, index) {
      this.locaGender = index
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less"></style>
