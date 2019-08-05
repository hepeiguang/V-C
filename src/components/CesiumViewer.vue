<template>
  <div id="cesiumContainer">
  </div>
</template>
<script type="text/javascript">
import Cesium from 'cesium/Cesium'
import widgets from 'cesium/Widgets/widgets.css'

export default {
  name: 'CesiumViewer',
  mounted () {
    var viewer = new Cesium.Viewer('cesiumContainer');
  }
}

</script>
<style  rel="stylesheet/css" lang="css" scoped>
#cesiumContainer {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
</style>