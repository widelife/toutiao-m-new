<template>
  <div class="search-history">
    <van-cell title="搜索历史">
      <div v-if="isDeleteShow">
        <span @click="$emit('quanbushanchu',[])">全部删除</span>
        &nbsp;&nbsp;
        <span @click="isDeleteShow = false">完成</span>
      </div>
      <van-icon @click="isDeleteShow = true" v-else name="delete" />
    </van-cell>
    <van-cell :title="item" v-for="(item, index) in seachHistories" :key="index" @click="onSeachItemClick(item, index)"><van-icon v-show="isDeleteShow" name="close" /></van-cell>
  </div>
</template>

<script>
export default {
  name: 'search-history',
  data() {
    return {
      isDeleteShow: false // 控制删除显示状态
    }
  },
  components: {},
  props: {
    // props数据
    // 如果是普通数据（数字字符串布尔值）绝对不能修改
    // 即使改了也不会传到给父组件

    // 如果是引用类型（数组对象）
    // 可以修改，如[].push
    // 不能冲新赋值[] = xxx
    seachHistories: {
      type: Array,
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    onSeachItemClick (item, index) {
      if (this.isDeleteShow) {
        // 删除状态
        this.seachHistories.splice(index, 1)
      } else {
        // 非删除状态直接进入搜索
        this.$emit('search', item)
      }
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less"></style>
