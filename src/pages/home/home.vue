<template>
    <div>
        <home-header :list="list"></home-header>
        <home-swiper :swiperList="swiperList"></home-swiper>
        <matchinfo :matchinfo="matchinfo"></matchinfo>
        <home-article :articles="articles"></home-article>
        <loading :isLoading="isLoading"></loading>
        <common-footer></common-footer>
    </div>
</template>

<script>
import homeHeader from 'common/components/header.vue'
import homeSwiper from './components/swiper.vue'
import homeArticle from './components/article.vue'
import matchinfo from 'common/components/matchinfo.vue'
import commonFooter from 'common/components/footer.vue'
import loading from 'common/components/loading.vue'
import axios from 'axios'
export default {
  name: 'Home',
  components: {
    homeHeader,
    homeSwiper,
    homeArticle,
    matchinfo,
    commonFooter,
    loading
  },
  data () {
    return {
      swiperList: [],
      articles: [],
      isLoading: false,
      timer: null,
      list: ['头条', '热门', '中超', '懂球号', '集锦', '英超', '西甲', '意甲', '德甲', '五洲', '深度', '专题'],
      matchinfo: []
    }
  },
  methods: {
    getHomeInfo () {
      axios.get('static/mock/home.json').then(this.getHomeInfoSucc)
    },
    getHomeInfoSucc (res) {
      res = res.data
      if (res.ret && res.data) {
        const data = res.data
        this.swiperList = data.swiperList
        this.articles = data.articles
        this.matchinfo = data.matchinfo
      }
    },
    handlescroll () {
      var totalHeight = document.documentElement.scrollHeight
      var scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
      var clientHeight = document.documentElement.clientHeight
      var bottom = totalHeight - scrollTop - clientHeight
      if (bottom < 50) {
        this.isLoading = true
        this.loadMore()
      }
    },
    loadMore () {
      var self = this
      if (this.timer) {
        clearTimeout(this.timer)
      }
      this.timer = setTimeout(() => {
        axios.get('static/mock/home.json').then(function (res) {
          res = res.data
          if (res.ret && res.data) {
            const moreData = res.data
            self.articles = self.articles.concat(moreData.articles)
            self.isLoading = false
          }
        })
      }, 1000)
    }
  },
  mounted () {
    this.getHomeInfo()
  },
  activated () { // keep-alive组件激活时调用
    window.addEventListener('scroll', this.handlescroll)
  },
  deactivated () { // keep-alive组件停用时调用
    // 绑定在window上的全局事件会在不同组件中触发，在离开当前组件时要对全局事件解绑
    // 跳转至另一个页面时要解除事件，防止在另一个页面触发当前页面的事件
    window.removeEventListener('scroll', this.handlescroll)
  }
}
</script>

<style lang="scss" scoped>
</style>
