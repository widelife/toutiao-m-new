<template>
  <div class="comment-reply">
    <van-nav-bar
      :title="
        comment.reply_count > 0 ? `${comment.reply_count}跳回复` : '暂无回复'
      "
    >
      <van-icon slot="left" name="cross" @click="$emit('close')" />
    </van-nav-bar>
    <div class="scroll-wrap">
      <!-- 当前评论 -->
      <CommentItem :comment="comment"></CommentItem>
      <!-- 评论回复全部 -->
      <van-cell title="全部回复" />
      <CommentList
        :list="commentList"
        :source="comment.com_id"
        type="c"
        @reply-click="onReplyClick"
      ></CommentList>
    </div>
    <!-- 发布pinglun -->
    <div class="post-wrap">
      <van-button
        size="small"
        round
        @click="isPostShow = true"
        class="post-btn"
      >
        写评论
      </van-button>
    </div>
    <!-- 写评论弹出层 -->
    <van-popup v-model="isPostShow" position="bottom">
      <CommentPost
        :target="comment.com_id"
        @post-success="onPostSuccess"
      ></CommentPost>
    </van-popup>
    <!-- 回复回复弹出层 -->
    <van-popup v-model="isReplyShow" position="bottom" style="height: 100%">
      <CommentReply
        v-if="isReplyShow"
        :comment="currentComment"
        @close="isReplyShow = false"
      ></CommentReply>
    </van-popup>
  </div>
</template>

<script>
import CommentItem from './comment-item.vue'
import CommentList from './comment-list.vue'
import CommentPost from './comment-post.vue'
export default {
  name: 'CommentReply',
  data() {
    return {
      isPostShow: false,
      commentList: [], // 评论回复列表
      isReplyShow: false,
      currentComment: {}
    }
  },
  components: {
    CommentItem,
    CommentList,
    CommentPost
  },
  props: {
    comment: {
      type: Object,
      require: true
    }
  },
  computed: {},
  watch: {},
  methods: {
    onPostSuccess(data) {
      // 更新回复数量
      this.comment.reply_count++
      //   关闭弹出层
      this.isPostShow = false
      //   将最新的回复内容展示到列表顶部
      this.commentList.unshift(data.new_obj)
    },
    onReplyClick(comment) {
      this.currentComment = comment
      this.isReplyShow = true
    }
  },
  created() {},
  mounted() {}
}
</script>

<style scoped lang="less">
.scroll-wrap {
  position: fixed;
  top: 92px;
  left: 0;
  right: 0;
  bottom: 88px;
  overflow-y: auto;
}
.post-wrap {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 88px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #fff;
  border-top: 1px solid #d8d8d8;
  .post-btn {
    width: 60%;
  }
}
</style>
