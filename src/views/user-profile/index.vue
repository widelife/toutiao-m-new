<template>
  <div class="user-profile">
    <!-- 导航栏 -->
    <van-nav-bar
      left-arrow
      @click-left="$router.back()"
      class="page-nav-bar"
      title="个人信息"
    ></van-nav-bar>
    <!-- 导航栏 -->
    <input type="file" hidden ref="file" @change="onFileChange" />
    <!-- 个人信息 -->
    <van-cell title="头像" is-link @click="$refs.file.click()">
      <van-image class="avater" fit="cover" round :src="user.photo" />
    </van-cell>
    <van-cell
      title="昵称"
      :value="user.name"
      is-link
      @click="isUpdateN = true"
    ></van-cell>
    <van-cell
      title="性别"
      :value="user.gender === 0 ? '难' : '钕'"
      is-link
      @click="isUpdateG = true"
    ></van-cell>
    <van-cell
      title="生日"
      :value="user.birthday"
      is-link
      @click="isUpdateB = true"
    ></van-cell>
    <!-- 个人信息 -->
    <!-- 编辑昵称弹出层 -->
    <van-popup v-model="isUpdateN" style="height: 100%" position="bottom">
      <UpdateName
        v-if="isUpdateN"
        @close="isUpdateN = false"
        v-model="user.name"
      ></UpdateName>
    </van-popup>
    <!-- 编辑昵称弹出层 -->
    <!-- 编辑性别弹出层 -->
    <van-popup v-model="isUpdateG" position="bottom">
      <UpdateGender
        @close="isUpdateG = false"
        v-model="user.gender"
        v-if="isUpdateG"
      ></UpdateGender>
    </van-popup>
    <!-- 编辑性别弹出层 -->
    <!-- 编辑生日弹出层 -->
    <van-popup v-model="isUpdateB" position="bottom">
      <UpdateBirthday
        v-model="user.birthday"
        @close="isUpdateB = false"
        v-if="isUpdateB"
      ></UpdateBirthday>
    </van-popup>
    <!-- 编辑生日弹出层 -->
    <!-- 编辑toux弹出层 -->
    <van-popup v-model="isUpdateP" position="bottom" style="height: 100%">
      <UpdatePhoto
        v-if="isUpdateP"
        :img="img"
        @close="isUpdateP = false"
        @update-photo="user.photo = $event"
      ></UpdatePhoto>
    </van-popup>
    <!-- 编辑toux弹出层 -->
  </div>
</template>

<script>
import { getUserProfile } from '@/api/user'
import UpdateName from './components/update-name.vue'
import UpdateGender from './components/update-gender.vue'
import UpdateBirthday from './components/update-birthday.vue'
import UpdatePhoto from './components/update-photo.vue'
export default {
  name: 'UserProfile',
  data() {
    return {
      user: {},
      isUpdateN: false,
      isUpdateG: false,
      isUpdateB: false,
      isUpdateP: false,
      img: null
    }
  },
  components: {
    UpdateName,
    UpdateGender,
    UpdateBirthday,
    UpdatePhoto
  },
  props: {},
  computed: {},
  watch: {},
  methods: {
    async loadUserProfile() {
      try {
        const { data } = await getUserProfile()
        this.user = data.data
      } catch (err) {
        this.$toast('获取个人资料失败')
      }
    },
    onFileChange() {
      // 获取文件对象
      const file = this.$refs.file.files[0]
      // 基于文章对象获取blob数据
      //   console.log(this.$refs.file.files)
      this.img = window.URL.createObjectURL(file)
      this.isUpdateP = true
      //   file-input如果选了同一张文件不会触发change事件
      // 解决办法是每次使用把他的value清空
      this.$refs.file.value = ''
    }
  },
  created() {
    this.loadUserProfile()
  },
  mounted() {}
}
</script>

<style scoped lang="less">
.user-profile {
  .avater {
    width: 60px;
    height: 60px;
  }
  .van-popup {
    background-color: #f5f7f9;
  }
}
</style>
