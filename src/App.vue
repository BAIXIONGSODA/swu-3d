<template>
  <div class="canvasContainer" ref="canvasContainer">
  </div>
</template>

<script setup>
import { onMounted, onUnmounted, ref } from "vue";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

const canvasContainer = ref(null);

onMounted(() => {
  // 初始化场景
  const scene = new THREE.Scene();

  // 初始化相机
  const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  camera.position.set(1.5, 1, 1.5);
  camera.aspect = window.innerWidth / window.innerHeight;
  // 更新相机投影矩阵
  camera.updateProjectionMatrix();

  // 加载背景图片
  const loader = new THREE.TextureLoader();
  const texture = loader.load('/assets/images/sky.jpg');
  texture.mapping = THREE.EquirectangularReflectionMapping;

  scene.background = texture;
  scene.environment = texture;

  // 加载模型
  const gltfLoader = new GLTFLoader();
  gltfLoader.load('/assets/models/scene.gltf', (gltf) => {
    let model = gltf.scene;
    console.log(model);
    // 设置模型的缩放
    scene.add(model);
  });

  // 初始化渲染器
  const renderer = new THREE.WebGLRenderer({ antialias: true });
  // 设置渲染器的大小
  renderer.setSize(window.innerWidth, window.innerHeight);
  // 监听窗口大小变化
  window.addEventListener('resize', () => {
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
  });

  // 将渲染器的dom元素添加到页面中
  canvasContainer.value.appendChild(renderer.domElement);

  // 添加控制器
  const controls = new OrbitControls(camera, renderer.domElement);
  // 设置控制器阻尼
  controls.enableDamping = true;


  // 渲染函数
  const render = () => {
    // 更新控制器
    controls.update();
    // 渲染场景
    renderer.render(scene, camera);
    // 循环渲染
    requestAnimationFrame(render);
  };

  // 开始渲染
  render();


});

onUnmounted(() => {
  
});

</script>

<style>
.canvasContainer {
  margin: 0;
  padding: 0;
}
</style>
