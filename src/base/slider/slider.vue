<template>
  <div class="slider" ref="slider">
    <div class="slider-group" ref="sliderGroup">
      <slot>
      </slot>
    </div>
    <div class="slider-dots">
      <span class="item-dot" 
            v-for="(entity, index) in dots"
            :class="{active:currentPageIndex === index}">
      </span>
    </div>
  </div>
</template>

<script>
import { addClass } from 'common/js/dom'
import BScroll from 'better-scroll'
export default {
  props: {
    loop: {
      type: Boolean,
      default: true
    },
    autoPlay: {
      type: Boolean,
      default: true
    },
    autoPlayInterval: {
      type: Number,
      default: 4000
    }
  },
  data() {
    return {
      dots: [],
      currentPageIndex: 0
    }
  },
  mounted() {
    // setTimeout(() => {
    this._setSliderWidth()
    this._initDots()
    this._initSlider()
    // if (this.autoPlay) {
    //   this._play()
    // }
    // }, 20)
    window.addEventListener('resize', () => {
      if (!this.slider) {
        return
      }
      this._setSliderWidth(true)
      this.slider.refresh()
    })
  },
  destoryed() {
    clearTimeout(this.timer)
  },
  methods: {
    _setSliderWidth(isResize) {
      let sliderWidth = this.$refs.slider.clientWidth
      let width = 0
      this.children = this.$refs.sliderGroup.children
      for (let i = 0; i < this.children.length; i++) {
        let child = this.children[i]
        addClass(child, 'item-slider')
        child.style.width = sliderWidth + 'px'
        width += sliderWidth
      }
      if (this.loop && !isResize) {
        width += sliderWidth * 2
      }
      this.$refs.sliderGroup.style.width = width + 'px'
    },
    _initSlider() {
      this.slider = new BScroll(this.$refs.slider, {
        scrollX: true,
        scrollY: false,
        momentum: false,
        snap: {
          loop: this.loop,
          threshold: 0.3,
          speed: 400
        },
        bounce: false,
        stopPropagation: true,
        click: true
      })
      this.slider.on('scrollEnd', () => {
        this.currentPageIndex = this.slider.getCurrentPage().pageX
        // console.log(this.slider.getCurrentPage().pageX)
        clearTimeout(this.timer)
        this._play()
      })
      if (this.autoPlay) {
        this._play()
      }
    },
    _initDots() {
      this.dots = new Array(this.children.length)
    },
    _play() {
      this.timer = setTimeout(() => {
        // if (this.currentPageIndex + 1 === this.dots.length) {
        //   this.currentPageIndex = -1
        // }
        this.slider.next()
      }, this.autoPlayInterval)
    }
  }
}
</script>

<style lang="stylus" scoped rel="stylesheet/stylus">
  @import "~common/stylus/variable"
  .slider
    min-height: 1px
    .slider-group
      overflow: hidden
      position: relative
      white-space: nowrap
      .item-slider
        float: left
        overflow: hidden
        a
          overflow: hidden
          width: 100%
          img
            overflow: hidden
            width: 100%
    .slider-dots
      position: absolute
      left: 0
      right: 0
      bottom: 12px
      text-align: center
      // font-size: 0
      .item-dot
        width: 8px
        height: 8px
        border-radius: 50%
        margin: 0 4px
        display: inline-block
        background: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>