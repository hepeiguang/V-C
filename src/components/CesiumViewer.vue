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
    viewer.cesiumWidget.screenSpaceEventHandler.removeInputAction(Cesium.ScreenSpaceEventType.LEFT_DOUBLE_CLICK);//屏蔽双击事件
    viewer.cesiumWidget.screenSpaceEventHandler.removeInputAction(Cesium.ScreenSpaceEventType.LEFT_CLICK);//屏蔽单击事件
    viewer.scene.screenSpaceCameraController.minimumZoomDistance = 25;        //相机的高度的最小值
    viewer.scene.screenSpaceCameraController.maximumZoomDistance = 22000000;  //相机高度的最大值

    console.log(this);
    this.loadGeoJson(viewer,'./static/geo.json');
    



//     禁止相机入地，存在bug
//     viewer.clock.onTick.addEventListener(function () {        
//         if(viewer.camera.pitch > 0){
//             viewer.scene.screenSpaceCameraController.enableTilt = false;
//         }
//     }); 
//     
//     var mousePosition,startMousePosition;
//     var handler = new Cesium.ScreenSpaceEventHandler(viewer.canvas);
//     handler.setInputAction(function(movement) { 
//         mousePosition=startMousePosition= Cesium.Cartesian3.clone(movement.position);
//         handler.setInputAction(function(movement) {
//             mousePosition = movement.endPosition;
//             var y = mousePosition.y - startMousePosition.y;
//             if(y>0){
//                 viewer.scene.screenSpaceCameraController.enableTilt = true;
//             }
//         }, Cesium.ScreenSpaceEventType.MOUSE_MOVE);
//     }, Cesium.ScreenSpaceEventType.MIDDLE_DOWN);

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

  },
  methods: {
    loadGeoJson: function (viewer,url) {
      // this.$http.get('./static/geo.json').then(function(res){
      //     console.log(res);
      // },function(){
      //     console.log('请求失败处理');
      // });
      var promise = Cesium.GeoJsonDataSource.load(url);    
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
    },

    


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