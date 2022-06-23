<template>
  <div class="update-photo">
    <img :src="img" alt="" class="img" ref="img" />
    <div class="toobar">
      <div class="cancel" @click="$emit('close')">取消</div>
      <div class="confirm" @click="onConfirm">完成</div>
    </div>
  </div>
</template>

<script>
import 'cropperjs/dist/cropper.css'
import Cropper from 'cropperjs'
import { updateUserPhoto } from '@/api/user'
export default {
  name: 'UpdatePhoto',
  data() {
    return {
      cropper: null
    }
  },
  components: {},
  props: {
    img: {
      type: [String, Object],
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    onConfirm() {
      // console.log(this.cropper.getData())
      this.cropper.getCroppedCanvas().toBlob(blob => {
        this.upDateUsertouxiang(blob)
      })
    },
    async upDateUsertouxiang(blob) {
      this.$toast.loading({
        message: '保存中...',
        forbidClick: true, // 禁止背景点击
        duration: 0 // 持续展示
      })
      try {
        // 错误用法
        // 如果接口要求Content-Type是application/json则传递普通js对象
        // updateUserPhoto({
        //   photo: blob
        // })
        //   如果接口要求Content-Type是multipart/form-data则传FormData对象
        //
        const formData = new FormData()

        // Pass the image file name as the third parameter ifnecessary.
        formData.append('photo', blob)
        const { data } = await updateUserPhoto(formData)
        console.log(data)
        this.$emit('close')
        this.$emit('update-photo', data.data.photo)
        this.$toast.success('更新成功')
      } catch (err) {
        this.$toast.fail('更新失败')
      }
    }
  },
  created() {},
  mounted() {
    const image = this.$refs.img
    this.cropper = new Cropper(image, {
      viewMode: 1,
      dragMode: 'move',
      aspectRatio: 1,
      autoCropArea: 1,
      cropBoxMovable: false,
      cropBoxResizable: false,
      background: false,
      movable: true
    })
  }
}
</script>

<style scoped lang="less">
.update-photo {
  background-color: #000;
  height: 100%;
  .toobar {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    .cancel,
    .confirm {
      width: 90px;
      height: 90px;
      font-size: 30px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
    }
  }
  .img {
    max-width: 100%;
    display: block;
  }
}
</style>
