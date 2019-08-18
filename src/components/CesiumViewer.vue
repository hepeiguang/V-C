<template>
  <div id="cesiumContainer"></div>
</template>
<script type="text/javascript">
import Cesium from "cesium/Cesium";
import widgets from "cesium/Widgets/widgets.css";
import axios from 'axios';

export default {
  name: "CesiumViewer",
  mounted() {
    var viewer = new Cesium.Viewer("cesiumContainer", {
      skyBox: false,
      skyAtmosphere: false,
      baseLayerPicker: false,
      shouldAnimate : true,
      imageryProvider: new Cesium.ArcGisMapServerImageryProvider({
        url:
          "http://map.geoq.cn/arcgis/rest/services/ChinaOnlineStreetPurplishBlue/MapServer"
      })
    });
    var canvas = viewer.scene.canvas;
    var ellipsoid = viewer.scene.globe.ellipsoid; 
    var viewArr = viewer.entities._entities._array;

    // this.$http.get('./static/geo.json').then(function(res){
    //     console.log(res);
    // },function(){
    //     console.log('请求失败处理');
    // });

    var promise = Cesium.GeoJsonDataSource.load('./static/geo.json');
    
    promise.then(function(dataSource) {
        viewer.dataSources.add(dataSource);

        //Get the array of entities
        var entities = dataSource.entities.values;
        //console.log(entities);
        var colorHash = {};
        for (let i = 0; i < entities.length; i++) {
            let entity = entities[i];
            let name = entity.properties._district;
            let color = colorHash[name];
            if (!color) {
                color = Cesium.Color.fromRandom({
                    alpha : 0.3
                });
                colorHash[name] = color;
            }
            entity.polygon.material = color;
            entity.polygon.outline = true;
            entity.polygon.outlineColor = color;
            entity.polygon.outlineWidth = 2;


            entity.polygon.extrudedHeight = entity.properties._height._value;
        }
    }).otherwise(function(error){
        //Display any errrors encountered while loading.
        window.alert(error);
    });

    viewer.clock.onTick.addEventListener(function(clock) {
      var camera = viewer.camera;
      //console.log(camera);
      if(camera.positionCartographic.height<=10){
        camera.positionCartographic.height=10
      }
      // x: -2184489.4993216135
      // y: 4389890.585845015
      // z: 4070281.4335553423
    });

    var handler = new Cesium.ScreenSpaceEventHandler(canvas);
    handler.setInputAction(function (movement) {
      let cartesian = viewer.scene.pickPosition(movement.position);
      let cartographic = ellipsoid.cartesianToCartographic(cartesian);
      let longitudeString = Cesium.Math.toDegrees(cartographic.longitude);
      let latitudeString = Cesium.Math.toDegrees(cartographic.latitude);
      let heightString = cartographic.height;

      var pickedFeature = viewer.scene.pick(movement.position);
      console.log(pickedFeature);
    }, Cesium.ScreenSpaceEventType.LEFT_CLICK);

    viewer.camera.setView({
        destination : {
        x: -2184489.4993216135,
        y: 4389890.585845015,
        z: 4070281.4335553423
      }, // 设置位置
        orientation: {
            heading : Cesium.Math.toRadians(0), // 方向
            pitch : Cesium.Math.toRadians(-45),// 倾斜角度
            roll : 0
        },
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