<template>
  <div id="threeCanvas"></div>
</template>

<script>
import * as THREE from 'three';
// import Tweakpane from 'tweakpane';
import Stats from 'three/examples/jsm/libs/stats.module';
import Tweakpane from 'tweakpane';

export default {
  data() {
    const stats = new Stats();
    const scene = new THREE.Scene();

    const camera = new THREE.PerspectiveCamera(
      60,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );

    const renderer = new THREE.WebGLRenderer();

    const planeGeometry = new THREE.PlaneGeometry(60, 40, 1, 1);
    const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xffffff });
    const plane = new THREE.Mesh(planeGeometry, planeMaterial);

    const step = 0;

    const pane = new Tweakpane();
    const params = {
      rotationSpeed: 0.02,
      numberOfObjects: scene.children.length,
    };
    pane.addInput(params, 'rotationSpeed', {
      min: 0,
      max: 0.5,
    });
    pane.addMonitor(params, 'numberOfObjects');
    pane
      .addButton({
        title: 'addCube',
      })
      .on('click', this.addCube);
    pane
      .addButton({
        title: 'removeCube',
      })
      .on('click', this.removeCube);
    pane
      .addButton({
        title: 'outputObjects',
      })
      .on('click', this.outputObjects);

    return { stats, scene, camera, renderer, plane, step, pane, params };
  },
  mounted() {
    this.renderer.setClearColor(new THREE.Color(0xeeeeee));
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.renderer.shadowMap.enabled = true;
    this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;

    this.scene.add(this.camera);

    this.plane.receiveShadow = true;
    this.plane.rotation.x = -0.5 * Math.PI;
    this.plane.position.set(0, 0, 0);

    this.scene.add(this.plane);

    this.camera.position.set(10, 40, -30);
    this.camera.lookAt(this.scene.position);

    const ambientLight = new THREE.SpotLight(0xffffff);
    this.scene.add(ambientLight);

    const spotLight = new THREE.SpotLight(0xffffff);
    spotLight.position.set(-20, 30, -5);
    spotLight.castShadow = true;
    spotLight.shadow.mapSize.width = 1024;
    spotLight.shadow.mapSize.height = 1024;
    this.scene.add(spotLight);

    this.params.numberOfObjects = this.scene.children.length;

    document
      .getElementById('threeCanvas')
      .appendChild(this.renderer.domElement);

    this.render();
  },
  methods: {
    render() {
      this.stats.update();

      this.scene.traverse((obj) => {
        if (obj instanceof THREE.Mesh && obj !== this.plane) {
          obj.rotation.x += this.params.rotationSpeed;
          obj.rotation.y += this.params.rotationSpeed;
          obj.rotation.z += this.params.rotationSpeed;
        }
      });

      requestAnimationFrame(this.render);
      this.renderer.render(this.scene, this.camera);
    },
    removeCube() {
      const allChildren = this.scene.children;
      const lastObject = allChildren[allChildren.length - 1];
      if (lastObject instanceof THREE.Mesh) {
        this.scene.remove(lastObject);
        this.params.numberOfObjects = this.scene.children.length;
      }
    },
    addCube() {
      const cubeSize = Math.ceil(Math.random() * 3);
      const cubeGeometry = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
      const cubeMaterial = new THREE.MeshLambertMaterial({
        color: Math.random() * 0xffffff,
      });
      const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
      cube.castShadow = true;
      cube.name = 'cube-' + this.scene.children.length;

      cube.position.x =
        -30 + Math.round(Math.random() * this.plane.geometry.parameters.width);
      cube.position.y = Math.round(Math.random() * 5);
      cube.position.z =
        -20 + Math.round(Math.random() * this.plane.geometry.parameters.height);

      this.scene.add(cube);
      this.params.numberOfObjects = this.scene.children.length;
    },
    outputObjects() {
      console.log(this.scene.children);
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
