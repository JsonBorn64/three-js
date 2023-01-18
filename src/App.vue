<template>
  <div ref="tree"></div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from './assets/OrbitControls';
import { GLTFLoader } from './assets/GLTFLoader.js';
import { RectAreaLightHelper } from './assets/RectAreaLightHelper.js'
import { RectAreaLightUniformsLib } from './assets/RectAreaLightUniformsLib.js';
export default {
  mounted() {
    //Scene
    const scene = new THREE.Scene()
    scene.background = new THREE.Color("#000");

    //Camera
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.set(0, 0.5, 1)

    //render
    const renderer = new THREE.WebGLRenderer({ antialias: true })
    renderer.setSize(window.innerWidth, window.innerHeight)
    this.$refs.tree.appendChild(renderer.domElement)

    // let plain;
    // {
    //   plain = new THREE.Mesh(
    //     new THREE.PlaneGeometry(1000, 1000),
    //     new THREE.MeshBasicMaterial({ color: "#000" })
    //   )
    //   plain.reciveShadow = true;
    //   plain.position.set(0, -1, 0)
    //   plain.rotateX(-Math.PI / 2);
    //   scene.add(plain)
    // }

    // Model
    {
      const loader = new GLTFLoader();
      loader.load('public/model/scene.gltf', gltf => {
        scene.add(gltf.scene);
      },
        function (error) {
          // console.log('Error: ' + error)
        }
      )
    }

    {
      const light = new THREE.DirectionalLight(0xffffff, 1)
      light.position.set(-2, 0, 10)
      light.lookAt(0, -1, 0)
      scene.add(light)

      // Helper
      // const helper = new THREE.DirectionalLightHelper(light, 5)
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
    controls.autoRotate = true;
    controls.autoRotateSpeed = 2;
    controls.enableDamping = true;
    controls.minDistance = 1;

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
</script>
  
<style scoped>

</style>
