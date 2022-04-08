<template>
  <div id="container">

  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import OrbitControls from 'three-orbitcontrols'
export default {
  name: 'HelloWorld',
  data () {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      controls: null
    }
  },
  mounted () {
    this.init();
  },
  methods: {
    init () {
      //  创建场景对象Scene
      this.scene = new THREE.Scene();
      // this.scene.backgroud = new THREE.Color(0x000000);
      //网格模型添加到场景中
      // let geometry = new THREE.BoxGeometry(0.2, 0.2, 0.2);
      // let material = new THREE.MeshNormalMaterial({
      //   color: "white"
      // });
      // this.mesh = new THREE.Mesh(geometry, material);
      // this.scene.add(this.mesh);

      //环绕光
      let ambientLight = new THREE.AmbientLight(0xaaaaaa, 0.3)
      this.scene.add(ambientLight);

      /**
       * 相机设置
       */
      let container = document.getElementById("container");
      this.camera = new THREE.PerspectiveCamera(
        30,
        container.clientWidth / container.clientHeight,
        0.01,
        1000
      );
      this.camera.position.set(3, 0.5, 0); //相机视角设置

      /**
       * 创建渲染器对象
       */
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);

      //创建控件对象
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      this.controls.addEventListener("change", this.renderer)
      const loader = new GLTFLoader();
      loader.load("Model/scene.gltf", (res) => {
        this.scenedom = res.scene;
        this.scenedom.position.set(0,-0.6,0);//模型位置定位
        this.scene.add(this.scenedom);
        // this.renderer.render(this.scene, this.camera)
        this.animate();
      });
    },
    animate () {
      this.scenedom.rotation.y += 0.01;
      this.renderer.render(this.scene, this.camera)
      requestAnimationFrame(this.animate)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#container {
  position: absolute;
  width: 100%;
  height: 100%;
}
</style>
