<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'

export default {
  name: "Scroll",
  props: {
    probeType: {
      type: Number,
      default: 0
    },
    pullUpLoad: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      scroll: null,
      message: '哈哈哈'
    }
  },
  mounted () {
    // 1.创建BScroll对象
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true,
      probeType: this.probeType,
      pullUpLoad: this.pullUpLoad
    })

    // 2.监听滚动的位置
    if (this.probeType === 2 || this.probeType === 3) {
      this.scroll.on('scroll', (position) => {
        // console.log(position);
        this.$emit('scroll', position)
      })
    }

    // console.log(this.scroll)
    /**
     * Better-Scroll在决定有多少区域可以滚动时，是根据scrollerHeight属性来计算的：
     *    scrollerHeight属性是根据放Better-Scroll的content中的子组件的高度
     *    但是在首页中，刚开始计算scrollerHeight时，是没有将图片计算在内的
     *    所以计算出来的高度是错误的，导致滚动出现问题
     *    后来图片加载进来之后，有了新的高度，但是scrollerHeight属性并没有进行更新，所以出现了问题
     *    所以：必须将图片加载完成以后，在进行计算，才能保证滚动正确
     * 
     */

    /**
     * 解决方案：
     * 1.监听每一张图片是否加载完成，只要有一张图片加载完成，就进行一次refresh
     * 2.对监听事件进行防抖（debounce）处理
     * 
     */


    //  3.监听上拉事件，是否触底
    if (this.pullUpLoad) {
      this.scroll.on('pullingUp', () => {
        this.$emit('pullingUp')
      })
    }
  },
  updated () {
    // console.log('updated')
  },
  activated () {
    // console.log('activated')
    this.scroll && this.scroll.enable()
  },
  methods: {
    scrollTo (x, y, time = 300) {
      this.scroll && this.scroll.scrollTo && this.scroll.scrollTo(x, y, time)
    },
    finishPullUp () {
      this.scroll.finishPullUp()
    },
    refresh () {
      // console.log(8888888888)
      this.scroll && this.scroll.refresh()
    },
    getScrollY () {
      return this.scroll ? this.scroll.y : 0
    }
  }
}
</script>

<style scoped></style>
