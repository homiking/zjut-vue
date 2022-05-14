<template>
  <div id="container">
  </div> 
</template>

<script>
import {BmlMarkerClusterer} from 'vue-baidu-map'
export default {
  components: {
    BmlMarkerClusterer
  },
  name:"Home",
  data () {
    return {
      
    }
  },
  mounted() {

    // 百度地图API功能
    var map = new BMap.Map("container"); 
    var point = new BMap.Point(120.03335, 30.22831);
    // 创建点坐标  
    map.centerAndZoom(point, 15);
    map.addControl(new BMap.NavigationControl());    
    map.addControl(new BMap.ScaleControl());    
    map.addControl(new BMap.OverviewMapControl());    
    map.addControl(new BMap.MapTypeControl());   
  },
}
</script>

<style>
.bm-view {
  width: 100%;
  height: 300px;
  }
</style>