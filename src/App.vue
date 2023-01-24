<template>
  <header class="header">
    <div class="logo">Logo</div>
    <nav class="menu">
      <li>Укропчик</li>
      <li>Помидорчики</li>
      <li>Капустка</li>
      <li>Картошечка</li>
      <li>Огурчики</li>
    </nav>
  </header>
  <section class="main_screen">
    <div class="offer">
      Тело что упало в лужу
      выпирается наружу
      силой выпертой воды
      телом впёртого туды
      <br>
      <br>
      Архимед ©
    </div>
    <div class="tree" ref="tree"></div>
  </section>
  <section class="second_screen"></section>
  <canvas id="sakura"></canvas>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from './assets/OrbitControls';
import { GLTFLoader } from './assets/GLTFLoader.js';
import { RectAreaLightHelper } from './assets/RectAreaLightHelper.js'
import { RectAreaLightUniformsLib } from './assets/RectAreaLightUniformsLib.js';
import sakura from '../public/sakura/sakura.js'

export default {
  mounted() {
    sakura()
    {//Scene
      const scene = new THREE.Scene()
      // scene.background = new THREE.Color("#333");
      //Camera
      const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      camera.position.set(0, 0, 1)
      //render
      const renderer = new THREE.WebGLRenderer({ antialias: true })
      renderer.setSize(window.innerWidth, window.innerHeight)
      renderer.setClearAlpha(0)
      this.$refs.tree.appendChild(renderer.domElement)
      // let floor;
      // {
      //   floor = new THREE.Mesh(
      //     new THREE.PlaneGeometry(1000, 1000),
      //     new THREE.MeshBasicMaterial({ color: "#ccc" })
      //   )
      //   floor.position.set(0, -150, 0)
      //   floor.rotateX(-Math.PI / 2);
      //   scene.add(floor)
      // }
      // Model
      {
        const loader = new GLTFLoader();
        loader.load('./tree/scene.gltf', gltf => {
          scene.add(gltf.scene);
          gltf.scene.position.set(150, -220, 100);
          let modelRotationX = 0;
          let modelRotationY = 0;
          window.addEventListener("mousemove", e => {
            const cursorX = e.clientX;
            const cursorY = e.clientY;
            const windowWidth = window.innerWidth;
            const windowHeight = window.innerHeight;
            const deviationX = cursorX - (windowWidth / 2);
            const deviationY = cursorY - (windowHeight / 2);
            modelRotationX = deviationY / (windowHeight / 2) * 0.05;
            modelRotationY = deviationX / (windowWidth / 2) * 0.1;
            gltf.scene.rotation.x = modelRotationX;
            gltf.scene.rotation.y = modelRotationY;
          });
        },
          function (error) {
            console.log(error)
          }
        )
      }
      {
        const light = new THREE.DirectionalLight(0xffffff, 1)
        light.position.set(-2, 0, 100)
        light.lookAt(0, -1, 0)
        scene.add(light)
        // Helper
        // const helper = new THREE.CameraHelper( light.shadow.camera );
        // scene.add(helper)
      }
      {
        const light = new THREE.DirectionalLight(0xffffff, 1)
        light.position.set(2, 0, 5)
        light.lookAt(0, 1, 0)
        scene.add(light)
        // Helper
        // const helper = new THREE.DirectionalLightHelper(light, 5)
        // scene.add(helper)
      }
      RectAreaLightUniformsLib.init();
      {
        const rectLight = new THREE.RectAreaLight(0xffffff, 1, 100, 100);
        rectLight.position.set(-10, 0, 0)
        rectLight.rotation.y = Math.PI + Math.PI / 4;
        scene.add(rectLight)
      }
      {
        const rectLight = new THREE.RectAreaLight(0xffffff, 1, 100, 100);
        rectLight.position.set(10, 0, 0)
        rectLight.rotation.y = Math.PI - Math.PI / 4;
        scene.add(rectLight)
      }
      //OrbitControls
      const controls = new OrbitControls(camera, renderer.domElement);
      // controls.autoRotate = true;
      // controls.autoRotateSpeed = 2;
      controls.enableDamping = true;
      controls.minDistance = 500;
      controls.enablePan = false;
      controls.enabled = false;
      //Resize
      window.addEventListener('resize', onWindowResize, false)
      function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight)
      }
      // Animate
      function animate() {
        requestAnimationFrame(animate)
        controls.update();
        renderer.render(scene, camera)
      }
      animate()
    }
  }
}
</script>
  
<style scoped>
.header {
  display: flex;
  justify-content: space-around;
  align-items: center;
  height: 100px;
}

.logo {
  font-size: 38px;
  font-weight: 900;
}

.menu {
  display: flex;
  justify-content: space-around;
  align-items: center;
  list-style: none;
  min-width: 600px;
}

.main_screen {
  display: flex;
  justify-content: center;
  align-items: center;
  height: calc(100vh - 100px)
}

.offer {
  font-size: 38px;
  max-width: 640px;
  line-height: 50px;
  margin-right: 400px;
  margin-bottom: 100px;
  letter-spacing: 10px;
  font-weight: 100;
  z-index: 1;
}

.tree {
  position: absolute;
  left: 0;
  top: 0;
}

#sakura {
  position: absolute;
  left: 0;
  top: 0;
  z-index: -10;
}

.second_screen {
  width: 100%;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.247);
}
</style>