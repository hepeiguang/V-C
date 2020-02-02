<template>
  <div class="hello">
    <!-- <h1>{{ msg }}</h1>
    <router-link to="/Cesium">{{ cesium }}</router-link> -->
    <div id="container"></div>
  </div>
</template>

<script>
import * as Three from 'three'
import OrbitControls from 'three-orbitcontrols'
import {MTLLoader, OBJLoader} from 'three-obj-mtl-loader'
import Stats from 'stats-js'

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'three.js哈哈哈测试',
      cesium: 'cesium',
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      stats:null,
      controls: null
    }
  },
  mounted() {
    this.init();
    this.animate()
  },
  created () {

  },
  methods: {
    init: function() {
        let container = document.getElementById('container');
        this.scene = new Three.Scene();

        this.camera = new Three.PerspectiveCamera(75, container.clientWidth/container.clientHeight, 0.01, 10000000);
        this.camera.position.z = 100;

        this.renderer = new Three.WebGLRenderer({antialias: true});//渲染器
        this.renderer.setSize(container.clientWidth, container.clientHeight);
        container.appendChild(this.renderer.domElement);

        let ambientLight = new Three.AmbientLight(0xdfebff);
        this.scene.add(ambientLight);

        let dirlight = new Three.DirectionalLight(0xdfebff, 1);
        dirlight.position.set(20, 20, 20);
        this.scene.add(dirlight);

        let orbitcontrols = new OrbitControls(this.camera, this.renderer.domElement);

        let axes = new Three.AxesHelper(1000000);
        this.scene.add(axes);

        this.controls = new OrbitControls(this.camera);

        this.stats = new Stats();//监测状态
        this.stats.domElement.style.position = 'absolute'; //绝对坐标  
        this.stats.domElement.style.left = '0px';// (0,0)px,左上角  
        this.stats.domElement.style.top = '0px';  
        document.getElementById('container').appendChild(this.stats.domElement);  

        let onProgress = function ( xhr ) {
					if ( xhr.lengthComputable ) {
						var percentComplete = xhr.loaded / xhr.total * 100;
						console.log( Math.round( percentComplete, 2 ) + '% downloaded' );
					}
				};
				let onError = function () { };

        // let mtlLoader = new MTLLoader();
        // let objLoader = new OBJLoader();
        // mtlLoader.load('/static/obj/guan.mtl', (materials) => {
        //   console.log(materials);
        //   materials.preload();
        //   objLoader.setMaterials(materials);
        //   objLoader.load('/static/obj/guan.obj', (object) => {
            
        //     //object.scale.multiplyScalar(1);
        //     this.scene.add(object);
        //   })
        // });

        var manager = new Three.LoadingManager();
        new MTLLoader( manager )
          .setPath( '/static/obj/' )
          .load( 'guan.mtl',  ( materials ) => {

            console.log(materials);
            materials.preload();
            new OBJLoader( manager )
							//.setMaterials( materials )
							.setPath( '/static/obj/' )
							.load( 'guan.obj', ( object ) => {
                //object.position.y = - 95;
								this.scene.add( object );
							}, onProgress, onError );
					} );

        let radius = 10, segemnt = 16, rings = 16;
        let sphereMaterial = new Three.MeshLambertMaterial({
          color: 0x999999,
          transparent: true,
          opacity: 0.8
        });
        let sphere = new Three.Mesh(
          new Three.SphereGeometry(radius, segemnt, rings),
          sphereMaterialy
        );
        sphere.geometry.verticesNeedUpdate = true;
        sphere.geometry.normalsNeedUpdate = true;
        //this.scene.add(sphere);

        console.log(this.scene);
 
        
 
    },
    animate: function() {
        requestAnimationFrame(this.animate);
        //this.mesh.rotation.x += 0.01;
        //this.mesh.rotation.y += 0.01;
        this.stats.update()
        this.controls.update()
        this.renderer.render(this.scene, this.camera);
    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a {
  color: #42b983;
}
#container {
    height: 400px;
}


</style>
