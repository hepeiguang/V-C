<template>
  <div id="cesiumContainer"></div>
</template>
<script type="text/javascript">
import Cesium from "cesium/Cesium";
import widgets from "cesium/Widgets/widgets.css";

export default {
  name: "CesiumViewer",
  mounted() {
    var viewer = new Cesium.Viewer("cesiumContainer", {
      skyBox: false,
      skyAtmosphere: false,
      baseLayerPicker: false,
      //shouldAnimate : true
      imageryProvider: new Cesium.ArcGisMapServerImageryProvider({
        url:
          "http://map.geoq.cn/arcgis/rest/services/ChinaOnlineStreetPurplishBlue/MapServer"
      })
    });
    


  }
};
</script>
<style  rel="stylesheet/css" lang="css" scoped>
#cesiumContainer {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
#cesiumContainer /deep/ .cesium-viewer-toolbar, 
#cesiumContainer /deep/ .cesium-viewer-animationContainer,
#cesiumContainer /deep/ .cesium-viewer-timelineContainer,
#cesiumContainer /deep/ .cesium-viewer-bottom{
  display: none !important;
}
#cesiumContainer /deep/ .cesium-viewer-fullscreenContainer .cesium-button{
  background-color: black !important;
}
</style>