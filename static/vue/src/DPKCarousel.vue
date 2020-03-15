<template>
  <div class="dpk-carousel">
    <ul :style="'width:' + (items.length * cWidth) + '%;height:' + carouselHeight + ';margin-left:-' + (position * cWidth) + '%;'">
      <li v-for="(item, i) in items" :key="'item-' + i.toString()">
        <slot :name="'item-' + i.toString()"></slot>
      </li>
    </ul>
    <div class="dpk-carousel-swiper-containder">
      <dpk-swiper v-on:swipe="onSwiped"></dpk-swiper>
    </div>
    <div class="dpk-carousel-marker-container">
      <a :class="position == i ? 'selected-marker' : ''" v-for="(item, i) in items" :key="'marker-' + i.toString()"></a>
    </div>
    <a v-if="position > 0" class="left-switch" v-on:click="jump(-1)">&lt;</a>
    <a v-if="position < items.length - 1" class="right-switch" v-on:click="jump(1)">&gt;</a>
  </div>
</template>
<script>
import {TweenLite} from 'gsap'
import DPKSwiper from './DPKSwiper.vue'
export default{
  components: {
    'dpk-swiper': DPKSwiper
  },
  props: ['items', 'height', 'contentwidth', 'autoplay', 'index'],
  data () {
    return {
      position: this.index,
      carouselHeight: this.height ? this.height.toString() + 'px' : '100%',
      cWidth: this.contentwidth ? this.contentwidth : 107,
      automaticallyPlay: this.autoplay
    }
  },
  watch: {
    index: function () {
      this.$data.position = this.index
    }
  },
  methods: {
    onSwiped: function (e) {
      let self = this
      if (e.direction === -1) {
        if (self.$data.position > 0) {
          self.jump(e.direction)
        }
      }
      if (e.direction === 1) {
        if (self.$data.position < self.items.length - 1) {
          self.jump(e.direction)
        }
      }
    },
    jump: function (dir) {
      let self = this
      self.$data.automaticallyPlay = false
      let nextVal = dir > 0 ? Math.floor(self.$data.position + dir) : Math.ceil(self.$data.position + dir)
      self.goToSlide(nextVal)
    },
    goToSlide: function (slide) {
      let self = this
      self.$emit('carousel-shift', {position: slide})
      TweenLite.to(self.$data, 0.5, {position: slide})
    },
    autoAdvance: function () {
      let self = this
      console.log(self.$data.automaticallyPlay)
      if (self.$data.automaticallyPlay) {
        let a = {n: 0}
        TweenLite.to(a, 5, {
          n: 1,
          onComplete: function (_self) {
            let nextSlide = _self.$data.position >= _self.items.length - 1 ? 0 : _self.$data.position + 1
            _self.goToSlide(nextSlide)
            if (nextSlide === 0) {
              _self.$data.automaticallyPlay = false
            }
            _self.autoAdvance()
          },
          onCompleteParams: [self]
        })
      }
    }
  },
  mounted: function () {
    let self = this
    self.autoAdvance()
  }
}
</script>
<style lang="scss" scoped>
div.dpk-carousel {
  div.dpk-carousel-swiper-containder{
    position:absolute;
    width:100%;
    height: 100%;
    top:0;
    left: 0;
  }
  /*height: 678px;*/
  width:100%;
  overflow:hidden;
  position:relative;
  > ul{
    margin:0;
    padding:0;
    height:194px;
    > li {
      margin:0;
      padding:0;
      display: inline-block;
    }
  }
  > a{
    position: absolute;
    display:block;
  }
  > a.left-switch{
    top: 10px;
    left: 0;
  }
  > a.right-switch{
    top: 10px;
    right: 0;
  }
  > div.dpk-carousel-marker-container{
    text-align: center;
    position: absolute;
    bottom: 0;
    width: 100%;
    > a{
      display: inline-block;
      width: 10px;
      height: 10px;
      border-radius: 10px;
      background-color: #cccccc;
      margin: 0 5px;
    }
    > a.selected-marker{
      background-color: #666666;
    }
  }
}
</style>