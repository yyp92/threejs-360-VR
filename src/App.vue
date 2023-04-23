<template>
  <div class="container" ref="container"></div>
</template>

<script setup>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader";
import { ref, onMounted } from "vue";
import gsap from "gsap";
import {
  CSS2DRenderer,
  CSS2DObject,
} from "three/examples/jsm/renderers/CSS2DRenderer.js";
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
camera.position.z = 0.1;
// 初始化渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);

const container = ref(null);

// 创建辅助坐标轴
// const axes = new THREE.AxesHelper(5);
// scene.add(axes);

const render = () => {
    renderer.render(scene, camera);
    requestAnimationFrame(render);
  };

// 挂载完毕之后获取dom
onMounted(() => {
  // 创建立方体
  const geometry = new THREE.BoxGeometry(10, 10, 10)
  geometry.scale(1, 1, -1)
  let textureUrl = './imgs/living/'
  let roomIndex = 4
  const arr = [
    `${roomIndex}_l`,
    `${roomIndex}_r`,
    `${roomIndex}_u`,
    `${roomIndex}_d`,
    `${roomIndex}_b`,
    `${roomIndex}_f`,
  ]

  //  盒子的材质数组
  const boxMaterials = []
  arr.forEach((item) => {
    // 加载纹理
    const texture = new THREE.TextureLoader().load(textureUrl + item + '.jpg')
    boxMaterials.push(new THREE.MeshBasicMaterial({
      map: texture
    }))

    const cube = new THREE.Mesh(geometry, boxMaterials)
    scene.add(cube)
  })
  
  container.value.appendChild(renderer.domElement)
  render();


  let isMouseDown = false
  // 监听鼠标按下事件
  container.value.addEventListener('mousedown', () => {
    isMouseDown = true
  }, false)
  container.value.addEventListener('mouseup', () => {
    isMouseDown = false
  }, false)
  container.value.addEventListener('mouseout', () => {
    isMouseDown = false
  }, false)

  // 是否按下鼠标，移动鼠标
  container.value.addEventListener('mousemove', (event) => {
    if (isMouseDown) {
      // event.movementX 鼠标移动距离
      camera.rotation.y += event.movementX * 0.01
      camera.rotation.x += event.movementY * 0.01
      // 修改旋转顺序 默认：xyz
      camera.rotation.order = 'YXZ'
    }
  }, false)
});

// 房间类
class Room {
  /**
   * name：房间名
   * roomIndex：房间索引
   * textureUrl：图片路径
   * position：位置
   * euler：旋转角度
  */
  constructor(
    name,
    roomIndex,
    textureUrl,
    position = new THREE.Vector3(0, 0, 0),
    euler = new THREE.Euler(0, 0, 0)
  ) {

  }
}

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
.label {
  color: #fff;
  text-shadow: 0 0 10px #000;
  font-size: 16px;
}
</style>
