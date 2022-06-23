<template>
  <div class="UpdateBirthday">
    <van-datetime-picker
      v-model="currentDate"
      type="date"
      title="选择年月日"
      :min-date="minDate"
      :max-date="maxDate"
      @cancel="$emit('close')"
      @confirm="onConfirm"
    />
  </div>
</template>

<script>
import { updateUserProfile } from '@/api/user'
import dayjs from 'dayjs'

export default {
  name: 'UpdateBirthday',
  data() {
    return {
      minDate: new Date(1969, 0, 1),
      maxDate: new Date(),
      currentDate: new Date(this.value)
    }
  },
  components: {},
  props: {
    value: {
      tpye: String,
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
        const currentDate = dayjs(this.currentDate).format('YYYY-MM-DD')
        await updateUserProfile({
          birthday: currentDate
        })
        //   console.log(data)
        //   更新视图
        this.$emit('input', currentDate)
        //   关闭弹层
        this.$emit('close')
        //   提示成功
        this.$toast.success('更新成功')
      } catch (err) {
        this.$toast('更新失败')
      }
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less"></style>
