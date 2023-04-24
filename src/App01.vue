<template>
  <div class="container" ref="container"></div>
</template>

<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader";
import { ref, onMounted } from "vue";
import gsap from "gsap";
// 初始化场景
const scene = new THREE.Scene();
// 初始化相机
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
// 设置相机位置
camera.position.set(0, 0, 0);
// 初始化渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);

const container = ref(null);

const render = () => {
  renderer.render(scene, camera);
  requestAnimationFrame(render);
};

// 创建辅助坐标轴
const axes = new THREE.AxesHelper(5);
scene.add(axes);

// 添加立方体
const geometry = new THREE.BoxGeometry(500, 500, 500);
geometry.scale(1, 1, -1);
// const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
// const cube = new THREE.Mesh(geometry, material);
// scene.add(cube);

// 4_b,
var arr = ["4_l", "4_r", "4_u", "4_d", "4_b", "4_f"];
var boxMaterials = [];

arr.forEach((item) => {
  // 纹理加载
  let texture = new THREE.TextureLoader().load(`./imgs/living/${item}.jpg`);
  // 创建材质
  if (item === "4_u" || item === "4_d") {
    texture.rotation = Math.PI;
    texture.center = new THREE.Vector2(0.5, 0.5);
    boxMaterials.push(new THREE.MeshBasicMaterial({ map: texture }));
  } else {
    boxMaterials.push(new THREE.MeshBasicMaterial({ map: texture }));
  }
});
const cube = new THREE.Mesh(geometry, boxMaterials);
// cube.geometry.scale(1, 1, -1);
scene.add(cube);

const geometry1 = new THREE.BoxGeometry(500, 500, 500);
geometry1.scale(1, 1, -1);
var arr1 = ["0_l", "0_r", "0_u", "0_d", "0_b", "0_f"];
var boxMaterials1 = [];

arr1.forEach((item) => {
  // 纹理加载
  let texture = new THREE.TextureLoader().load(`./imgs/kitchen/${item}.jpg`);
  // boxMaterials1.push(new THREE.MeshBasicMaterial({ map: texture }));
  // 创建材质
  if (item === "0_u" || item === "0_d") {
    texture.rotation = Math.PI;
    texture.center = new THREE.Vector2(0.5, 0.5);
    boxMaterials1.push(new THREE.MeshBasicMaterial({ map: texture }));
  } else {
    boxMaterials1.push(new THREE.MeshBasicMaterial({ map: texture }));
  }
});
const cube1 = new THREE.Mesh(geometry1, boxMaterials1);
cube1.position.set(500, 0, -500);
scene.add(cube1);

// 添加小球
const sphereGeometry = new THREE.SphereGeometry(5, 32, 32);
const sphereMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 });
const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
sphere.position.set(250, 0, -150);
scene.add(sphere);

// 鼠标点击小球事件
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();

// 添加球
// const geometry = new THREE.SphereGeometry(5, 32, 32);
// const loader = new RGBELoader();
// loader.load("./imgs/hdr/Living.hdr", (texture) => {
//   const material = new THREE.MeshBasicMaterial({ map: texture });
//   const sphere = new THREE.Mesh(geometry, material);
//   sphere.geometry.scale(1, 1, -1);
//   scene.add(sphere);
// });

// 挂载完毕之后获取dom
onMounted(() => {
  // 添加控制器
  const controls = new OrbitControls(camera, container.value);
  controls.enableDamping = true;
  container.value.appendChild(renderer.domElement);
  render();

  const onClick = (event) => {
    mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
    mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    raycaster.setFromCamera(mouse, camera);
    // const intersects = raycaster.intersectObjects(scene.children);
    const intersects = raycaster.intersectObject(sphere);
    if (intersects.length > 0) {
      console.log(intersects);
      gsap.to(camera.position, {
        duration: 1,
        x: cube1.position.x + 1,
        y: cube1.position.y + 1,
        z: cube1.position.z + 1,
      });
      gsap.to(controls.target, {
        duration: 1,
        x: cube1.position.x,
        y: cube1.position.y,
        z: cube1.position.z,
      });
    }
  };
  window.addEventListener("click", onClick);
});
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
.container {
  height: 100vh;
  width: 100vw;
  background-color: #f0f0f0;
}
</style>
