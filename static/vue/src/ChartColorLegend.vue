<template>
  <ul :class="horizontal ? 'horzontal-legend chart-legend' : 'chart-legend'">
    <li v-for="(label, i) in labels" :key="'label-' + i.toString()">
      <span :style="'background-color:' + colors[i] + ';'"></span><span>{{label}}</span>
    </li>
  </ul>
</template>
<script>
export default {
  props: ['colors', 'labels', 'horizontal']
}
</script>
<style lang="scss" scoped>
ul.chart-legend{
  padding: 0;
  margin:0 auto;
  > li{
    position:relative;
    display:block;
    > span:first-child{
      display:inline-block;
      width: 20px;
      height: 20px;
      border-radius: 20px;
      position: absolute;
      top:0;
      left:0;
    }
    > span:last-child{
      display:inline-block;
      font-size: 12px;
      margin-left: 23px;
      margin-top:-8px;
      margin-right: 12px;
    }
  }
}
ul.horzontal-legend{
  > li{
    display: inline-block;
    float: left;
  }
}
</style>