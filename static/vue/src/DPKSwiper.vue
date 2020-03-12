<template>
  <div :id="swiperID" class="dpk-swiper">
    <div :class="swiping ? 'swipe-mode' : ''">
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      swiping: false,
      positions: [],
      threshhold: 100,
      swiperID: 'swpiper-' + Math.random().toString().split('.').join('') + '-' + Math.random().toString().split('.').join('') + '-' + Math.random().toString().split('.').join(''),
      swipeTime: {start: 0, end: 0}
    }
  },
  methods: {
    processEvent: function (e) {
      console.log(e.touches[0].screenX)
      return {x: e.touches[0].screenX, y: e.touches[0].screenY}
    },
    onDown: function (e) {
      let self = this
      self.$data.swipeTime.start = new Date().getTime()
      self.$data.swiping = true
      self.$data.positions.push(self.processEvent(e))
      console.log('start')
    },
    onMove: function (e) {
      let self = this
      self.$data.positions.push(self.processEvent(e))
      console.log('moving')
    },
    onUp: function (e) {
      let self = this
      self.$data.swiping = false
      // self.$data.positions.push(self.processEvent(e))
      let xDist = self.$data.positions[0].x - self.$data.positions[self.$data.positions.length - 1].x
      let yDist = self.$data.positions[0].y - self.$data.positions[self.$data.positions.length - 1].y
      self.$data.swipeTime.end = new Date().getTime()
      if (Math.abs(xDist) > Math.abs(yDist) && Math.abs(xDist) > self.$data.threshhold && self.$data.swipeTime.end - self.$data.swipeTime.start < 1000) {
        let dir = xDist > 0 ? 1 : -1
        self.$emit('swipe', {direction: dir})
        console.log(dir)
      }
      self.$data.positions = []
      self.$data.swiping = false
      console.log('end')
    }
  },
  mounted: function () {
    let self = this
    document.querySelector('#' + self.$data.swiperID + ' > div').addEventListener('touchstart', self.onDown)
    document.querySelector('#' + self.$data.swiperID + ' > div').addEventListener('touchmove', self.onMove)
    document.querySelector('#' + self.$data.swiperID + ' > div').addEventListener('touchend', self.onUp)
  }
}
</script>
<style lang="scss" scoped>
  div.dpk-swiper{
    width: 100%;
    height: 100%;
    > div{
      width: 100%;
      height: 100%;
    }
    > div.swipe-mode{
      position:fixed;
      left:0;
      top:0;
      right:0;
      bottom:0;
      display:none;
      background-color: #cccccc;
    }
  }
</style>