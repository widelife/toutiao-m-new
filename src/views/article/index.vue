<template>
  <div class="article-container">
    <!-- 导航栏 -->
    <van-nav-bar
      class="page-nav-bar"
      left-arrow
      title="黑马头条"
    ></van-nav-bar>
    <!-- /导航栏 -->

    <div class="main-wrap">
      <!-- 加载中 -->
      <div
        v-if="loading"
        class="loading-wrap"
      >
        <van-loading
          color="#3296fa"
          vertical
        >加载中</van-loading>
      </div>
      <!-- /加载中 -->

      <!-- 加载完成-文章详情 -->
      <div
        v-else-if="article.title"
        class="article-detail"
      >
        <!-- 文章标题 -->
        <h1 class="article-title">{{ article.title }}</h1>
        <!-- /文章标题 -->

        <!-- 用户信息 -->
        <van-cell
          class="user-info"
          center
          :border="false"
        >
          <van-image
            class="avatar"
            slot="icon"
            round
            fit="cover"
            :src="article.aut_photo"
          />
          <div
            slot="title"
            class="user-name"
          >{{ article.aut_name }}</div>
          <div
            slot="label"
            class="publish-date"
          >
            {{ article.pubdate | relativeTime }}
          </div>
          <!-- 1.模板中的$event就是事件参数
          2.当我们传递给子组件的数据季要用又要修改
          传递：props
          :is-followed="article.is_followed"
          修改：自定义事件
          @update-followed="article.is_followed = $event"
          简写方式
          在组件上使用v-model
          value="article.is_followed"
          @input="article.is_followed = $event"
          3.如果多个数据需要实现类似于v-model的效果，可以使用属性的。sync修饰符-->
          <follow-user
            class="follow-btn"
            v-model="article.is_followed"
            :user-id="article.aut_id"
          />
          <!-- <van-button
            v-if="article.is_followed"
            class="follow-btn"
            round
            size="small"
            :loading="followLoading"
            @click="onFollow"
            >已关注</van-button
          >
          <van-button
            v-else
            :loading="followLoading"
            @click="onFollow"
            class="follow-btn"
            type="info"
            color="#3296fa"
            round
            size="small"
            icon="plus"
            >关注</van-button
          > -->
        </van-cell>
        <!-- /用户信息 -->

        <!-- 文章内容 -->
        <div
          class="article-content markdown-body"
          v-html="article.content"
          ref="article-content"
        >
          这是文章内容
        </div>
        <van-divider>正文结束</van-divider>
        <!-- 文章评论列表 -->
        <CommentList
          :source="article.art_id"
          :list="commentList"
          @onload-success="totalCmt = $event.total_count"
          @reply-click="onReplyClick"
        ></CommentList>
        <!-- 文章评论列表 -->
      </div>
      <!-- /加载完成-文章详情 -->

      <!-- 加载失败：404 -->
      <div
        v-else-if="errStatus === 404"
        class="error-wrap"
      >
        <van-icon name="failure" />
        <p class="text">该资源不存在或已删除！</p>
      </div>
      <!-- /加载失败：404 -->

      <!-- 加载失败：其它未知错误（例如网络原因或服务端异常） -->
      <div
        v-else
        class="error-wrap"
      >
        <van-icon name="failure" />
        <p class="text">内容加载失败！</p>
        <van-button
          @click="loadArticle"
          class="retry-btn"
        >点击重试</van-button>
      </div>
      <!-- /加载失败：其它未知错误（例如网络原因或服务端异常） -->
    </div>

    <!-- 底部区域 -->
    <div class="article-bottom">
      <van-button
        class="comment-btn"
        type="default"
        round
        size="small"
        @click="isPostShow = true"
      >写评论</van-button>
      <van-icon
        name="comment-o"
        :info="totalCmt"
        color="#777"
      />
      <CollectArticle
        v-model="article.is_collected"
        :articleId="article.art_id"
      ></CollectArticle>
      <!-- <van-icon color="#777" name="star-o" /> -->
      <LikeArticle
        v-model="article.attitude"
        :articleId="article.art_id"
      ></LikeArticle>
      <!-- <van-icon color="#777" name="good-job-o" /> -->
      <van-icon
        name="share"
        color="#777777"
      ></van-icon>
    </div>
    <!-- /底部区域 -->
    <!-- 发布评论 -->
    <van-popup
      v-model="isPostShow"
      position="bottom"
    >
      <CommentPost
        :target="article.art_id"
        @post-success="onPostSuccsess"
      ></CommentPost>
    </van-popup>
    <!-- 发布评论 -->
    <!-- 评论回复 -->
    <!-- 弹出层是懒渲染的只有在第一次展示的时候才会渲染里面的内容 -->
    <van-popup
      v-model="isReplyShow"
      position="bottom"
      style="height: 100%"
    >
      <CommentReply
        v-if="isReplyShow"
        :comment="currentComment"
        @close="isReplyShow = false"
      ></CommentReply>
    </van-popup>
    <!-- 评论回复 -->
  </div>
</template>

<script>
import { getArticleById } from '@/api/article'
import { ImagePreview } from 'vant'
import FollowUser from '@/components/follow-user'
import CollectArticle from '@/components/collect-article'
import LikeArticle from '@/components/like-article'
import CommentList from './components/comment-list.vue'
import CommentPost from './components/comment-post.vue'
import CommentReply from './components/comment-reply.vue'
export default {
  name: 'ArticleIndex',
  components: {
    FollowUser,
    CollectArticle,
    LikeArticle,
    CommentList,
    CommentPost,
    CommentReply
  },
  // 给所有后代组件提供数据
  // 不要滥用
  provide: function () {
    return {
      articleId: this.articleId
    }
  },
  props: {
    articleId: {
      type: [Number, String, Object],
      required: true
    }
  },
  data () {
    return {
      article: {}, // 文章详情
      loading: true, // 加载中的loading状态
      errStatus: 0, // 失败的状态码
      totalCmt: 0, // 评论总数
      isPostShow: false, // 控制发布评论的显示状态
      commentList: [],
      isReplyShow: false,
      currentComment: {} // 当前点击回复的评论项
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadArticle()
  },
  mounted () { },
  methods: {
    async loadArticle () {
      this.loading = true
      try {
        const { data } = await getArticleById(this.articleId)
        // if (Math.random() > 0.5) {
        //   JSON.parse('afasgfasdasdaghcasraca')
        // }
        // 数据驱动视图这个事件不是立即的
        this.article = data.data
        // console.log(this.$refs['article-content'])
        setTimeout(() => {
          this.previewImg()
        }, 0)
        // this.loading = false
      } catch (err) {
        if (err.response && err.response.status === 404) {
          this.errStatus = 404
        }
        console.log('获取详情失败', err)
        // this.loading = false
      }
      this.loading = false
    },
    previewImg () {
      const articleContent = this.$refs['article-content']
      const imgs = articleContent.querySelectorAll('img')
      const images = []
      imgs.forEach((img, index) => {
        images.push(img.src)
        img.onclick = () => {
          ImagePreview({
            images,
            startPosition: index
            // closeable: true
          })
        }
      })
      //   console.log(images)
    },
    onPostSuccsess (data) {
      // 关闭弹出层
      this.isPostShow = false
      // 将发布内容添加到列表的顶部
      this.commentList.unshift(data.new_obj)
    },
    onReplyClick (comment) {
      this.currentComment = comment
      this.isReplyShow = true
    }
  }
}
</script>

<style scoped lang="less">
@import './github-markdown.css';
.article-container {
  .main-wrap {
    position: fixed;
    left: 0;
    right: 0;
    top: 92px;
    bottom: 88px;
    overflow-y: scroll;
    background-color: #fff;
  }
  .article-detail {
    .article-title {
      font-size: 40px;
      padding: 50px 32px;
      margin: 0;
      color: #3a3a3a;
    }

    .user-info {
      padding: 0 32px;
      .avatar {
        width: 70px;
        height: 70px;
        margin-right: 17px;
      }
      .van-cell__label {
        margin-top: 0;
      }
      .user-name {
        font-size: 24px;
        color: #3a3a3a;
      }
      .publish-date {
        font-size: 23px;
        color: #b7b7b7;
      }
      .follow-btn {
        width: 170px;
        height: 58px;
      }
    }

    .article-content {
      padding: 55px 32px;
      /deep/ p {
        text-align: justify;
      }
    }
  }

  .loading-wrap {
    padding: 200px 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #fff;
  }

  .error-wrap {
    padding: 200px 32px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: #fff;
    .van-icon {
      font-size: 122px;
      color: #b4b4b4;
    }
    .text {
      font-size: 30px;
      color: #666666;
      margin: 33px 0 46px;
    }
    .retry-btn {
      width: 280px;
      height: 70px;
      line-height: 70px;
      border: 1px solid #c3c3c3;
      font-size: 30px;
      color: #666666;
    }
  }

  .article-bottom {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    display: flex;
    justify-content: space-around;
    align-items: center;
    box-sizing: border-box;
    height: 88px;
    border-top: 1px solid #d8d8d8;
    background-color: #fff;
    .comment-btn {
      width: 282px;
      height: 46px;
      border: 2px solid #eeeeee;
      font-size: 30px;
      line-height: 46px;
      color: #a7a7a7;
    }
    .van-icon {
      font-size: 40px;
      .van-info {
        font-size: 16px;
        background-color: #e22829;
      }
    }
  }
}
</style>
