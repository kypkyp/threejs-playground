<template>
  <canvas id="threeCanvas"></canvas>
</template>

<script>
import * as THREE from 'three';
import Stats from 'three/examples/jsm/libs/stats.module';

export default {
  data() {
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      45,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    const renderer = null;
    const stats = new Stats();
    stats.setMode(0);
    stats.domElement.style.position = 'absolute';
    stats.domElement.style.left = '0px';
    stats.domElement.style.top = '0px';
    document.body.appendChild(stats.dom);

    const cubeGeometry = new THREE.BoxGeometry(4, 4, 4);
    const cubeMaterial = new THREE.MeshLambertMaterial({
      color: 0xff0000,
    });
    const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);

    const sphereGeometry = new THREE.SphereGeometry(4, 20, 20);
    const sphereMaterial = new THREE.MeshLambertMaterial({
      color: 0x7777ff,
    });
    const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);

    const step = 0;

    return { scene, camera, renderer, stats, cube, sphere, step };
  },
  mounted() {
    this.renderer = new THREE.WebGLRenderer({
      canvas: document.getElementById('threeCanvas'),
    });
    this.renderer.setClearColor(new THREE.Color(0xeeeeee));
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.renderer.setPixelRatio(window.devicePixelRatio);
    this.renderer.shadowMap.enabled = true;
    this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;

    const axes = new THREE.AxesHelper(20);
    this.scene.add(axes);

    const spotLight = new THREE.SpotLight(0xffffff);
    spotLight.position.set(-20, 30, -5);
    spotLight.castShadow = true;
    spotLight.shadow.mapSize.width = 1024;
    spotLight.shadow.mapSize.height = 1024;
    this.scene.add(spotLight);

    const planeGeometry = new THREE.PlaneGeometry(60, 20);
    const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xcccccc });
    const plane = new THREE.Mesh(planeGeometry, planeMaterial);

    plane.rotation.x = -0.5 * Math.PI;
    plane.position.x = 15;
    plane.position.y = 0;
    plane.position.z = 0;
    plane.receiveShadow = true;

    this.scene.add(plane);

    this.cube.position.x = -4;
    this.cube.position.y = 3;
    this.cube.position.z = 0;
    this.cube.castShadow = true;

    this.scene.add(this.cube);

    this.sphere.position.x = 20;
    this.sphere.position.y = 4;
    this.sphere.position.z = 2;
    this.sphere.castShadow = true;

    this.scene.add(this.sphere);

    this.camera.position.x = -30;
    this.camera.position.y = 40;
    this.camera.position.z = 30;
    this.camera.lookAt(this.scene.position);

    this.renderScene();
  },
  methods: {
    renderScene() {
      this.stats.update();

      this.cube.rotation.x += 0.02;
      this.cube.rotation.y += 0.02;
      this.cube.rotation.z += 0.02;

      this.step += 0.04;
      this.sphere.position.x = 20 + 10 * Math.cos(this.step);
      this.sphere.position.y = 2 + 10 * Math.abs(Math.sin(this.step));

      requestAnimationFrame(this.renderScene);
      this.renderer.render(this.scene, this.camera);
    },
  },
};
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
}
</style>
