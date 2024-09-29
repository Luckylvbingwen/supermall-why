<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <tab-control ref="tabControl1" class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"
      v-show="isTabFixed" />
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll" :pull-up-load="true"
      @pullingUp="loadMore">
      <home-swiper :banners="banners" @swiperImageLoad="swiperImageLoad" />
      <recommend-view :recommends="recommends" />
      <feature-view />
      <tab-control ref="tabControl2" class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick" />
      <good-list :goods="showGoods" />
    </scroll>
    <!-- <div>呵呵呵呵</div> -->
    <!-- native修饰符：在需要监听一个组件的原生事件时，必须给对应的事件加上.native修饰符，才能进行监听 -->
    <back-top @click.native="backClick" v-show="isShowBackTop" />
  </div>
</template>

<script>
import HomeSwiper from './childComps/HomeSwiper'
import RecommendView from './childComps/RecommendView'
import FeatureView from './childComps/FeatureView'

import NavBar from 'components/common/navbar/NavBar'
import TabControl from 'components/content/tabControl/TabControl'
import GoodList from 'components/content/goods/GoodsList'
import Scroll from 'components/common/scroll/Scroll'
import BackTop from 'components/content/backTop/BackTop'

import { getHomeMultidata, getHomeGoods } from "network/home"
import { debounce } from 'common/utils'

export default {
  name: "Home",
  components: {
    HomeSwiper,
    RecommendView,
    FeatureView,
    NavBar,
    TabControl,
    GoodList,
    Scroll,
    BackTop
  },
  data () {
    return {
      banners: [],
      recommends: [],
      goods: {
        'pop': { page: 0, list: [] },
        'new': { page: 0, list: [] },
        'sell': { page: 0, list: [] },
      },
      currentType: 'pop',
      isShowBackTop: false,
      tabOffsetTop: 0,
      isTabFixed: false,
      saveY: 0,
    }
  },
  computed: {
    showGoods () {
      // 数据接口无数据，暂时使用本地数据
      // return this.goods[this.currentType].list
      const list = [
        {
          id: 1,
          cfav: '3',
          price: 3000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 2,
          cfav: '4',
          price: 4400,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 3,
          cfav: '5',
          price: 5000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 4,
          cfav: '6',
          price: 6000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 5,
          cfav: '7',
          price: 7000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 6,
          cfav: '8',
          price: 8000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 7,
          cfav: '9',
          price: 9000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 8,
          cfav: '10',
          price: 10000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 9,
          cfav: '11',
          price: 11000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 10,
          cfav: '12',
          price: 12000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 11,
          cfav: '13',
          price: 13000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 12,
          cfav: '14',
          price: 14000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 13,
          cfav: '15',
          price: 15000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        },
        {
          id: 14,
          cfav: '16',
          price: 16000,
          title: '随便写写',
          show: {
            img: require('../../assets/img/home/recommend_bg.jpg')
          }
        }
      ]
      return list
    }
  },
  activated () {
    // console.log('activated')
    // 1.回来时，直接让页面滚动到记录的位置
    this.$refs.scroll.scrollTo(0, this.saveY, 0)

    // 2.回来时，刷新一下页面
    this.$refs.scroll.refresh()
  },
  deactivated () {
    // console.log('deactivated')
    // 1.离开时，保存Y值
    this.saveY = this.$refs.scroll.getScrollY()

    // 2.离开时，取消全局事件的监听
    // this.$bus.$off('itemImageLoad', this.itemImgListener)
  },
  created () {
    // 1.请求多个数据
    this.getHomeMultidata()

    // 2.请求商品数据
    this.getHomeGoods('pop')
    this.getHomeGoods('new')
    this.getHomeGoods('sell')


  },
  mounted () {
    // 3.监听item中图片加载完成
    // 4.对refresh函数进行防抖
    const refresh = debounce(this.$refs.scroll.refresh, 100)
    this.$bus.$on('itemImageLoad', () => {
      // console.log('--------')
      refresh()
    })

  },
  methods: {
    /**
     * 事件监听相关的方法
     */
    tabClick (index) {
      switch (index) {
        case 0:
          this.currentType = 'pop'
          break
        case 1:
          this.currentType = 'new'
          break
        case 2:
          this.currentType = 'sell'
          break
      }

      this.$refs.tabControl1.currentIndex = index
      this.$refs.tabControl2.currentIndex = index
    },
    backClick () {
      this.$refs.scroll.scrollTo(0, 0)
    },
    contentScroll (position) {
      // 1. 判断BackTop是否显示
      this.isShowBackTop = (-position.y) > 1000

      // 2. 判断tabControl是否吸顶(position: fixed)
      this.isTabFixed = (-position.y) > this.tabOffsetTop
    },
    loadMore () {
      // console.log('上拉加载更多')
      this.getHomeGoods(this.currentType)

    },
    swiperImageLoad () {
      // console.log('swiperImageLoad')

      // 获取tabControl的offsetTop
      // 所有的组件都有一个属性$el: 用于获取组件中的元素
      // console.log(this.$refs)
      // console.log(this.$refs.tabControl?.$el.offsetTop)
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop
    },
    /**
     * 网络请求相关的方法
     */
    getHomeMultidata () {
      getHomeMultidata().then(res => {
        // this.result = res;
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      })
    },
    getHomeGoods (type) {
      const page = this.goods[type].page + 1
      getHomeGoods(type, page).then(res => {
        // this.goods[type]?.list.push(...res.data.list)
        // this.goods[type]?.page += 1

        // 完成上拉加载更多
        this.$refs.scroll.finishPullUp()
      })
    }
  }
}
</script>

<style scoped>
#home {
  /*padding-top: 44px;*/
  height: 100vh;
  position: relative;
}

.home-nav {
  background-color: var(--color-tint);
  color: #fff;

  /* position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9; */
}

.tab-control {
  /* tab吸顶效果 */
  /* position: sticky;
  top: 44px;
  z-index: 9; */

  position: relative;
  z-index: 99;
}

.content {
  overflow: hidden;

  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}

/* .content {
  height: calc(100% - 93px);
  overflow: hidden;
  margin-top: 44px;
} */
</style>
