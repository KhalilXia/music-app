<template>
  <div class="recommend">
    <div class="slider-wrapper" v-if="sliderList.length">
      <keep-alive>
      	<slider>
            <div v-for="entity in sliderList">
              <a :href="entity.linkUrl">
                <img :src="entity.picUrl" alt="">
              </a>
            </div> 
        </slider>
      </keep-alive>
    </div>

  </div>  
</template>

<script>
import { getRecommend, ERR_OK } from 'api/recommend.js'
import Slider from 'base/slider/slider'
export default {
  data() {
    return {
      sliderList: []
    }
  },
  mounted() {
    this._getRecommend()
  },
  methods: {
    _getRecommend() {
      getRecommend().then((res) => {
        // console.log(data)
        if (res.code === ERR_OK) {
          this.sliderList = res.data.slider
        }
      })
    }
  },
  components: {
    Slider
  }
}
</script>

<style lang="stylus" scoped rel="stylesheet/stylus">
  .recommend
    .slider-wrapper
      overflow: hidden
      width: 100%
      position: relative
</style>