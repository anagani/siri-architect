<template>
  <div
    ref="whirlpoolCanvasContainerRef"
    :class="cn('pointer-events-none fixed inset-0 z-10', $props.class)"
  >
    <canvas
      ref="whirlpoolCanvasRef"
      class="size-full"
    ></canvas>
  </div>
</template>

<script lang="ts" setup>
import {
  Vector3,
  MathUtils,
  AmbientLight,
  BoxGeometry,
  InstancedMesh,
  WebGLRenderer,
  Scene,
  PerspectiveCamera,
  Object3D,
  Vector2,
  Raycaster,
  Plane,
  MeshBasicMaterial,
  InstancedBufferAttribute,
} from "three";
import { onMounted, onUnmounted, ref } from "vue";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { UnrealBloomPass } from "three/addons/postprocessing/UnrealBloomPass.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { cn } from "@/lib/utils";

const { randFloat: rnd, randFloatSpread: rndFS } = MathUtils;

type Instance = {
  position: Vector3;
  scale: number;
  scaleZ: number;
  velocity: Vector3;
  attraction: number;
  vLimit: number;
};

const props = defineProps({
  particleCount: {
    type: Number,
    default: 400,
  },
  class: String,
  blur: {
    type: Number,
    default: 0,
  },
});

const instances: Instance[] = [];
const target = new Vector3();
const dummyO = new Object3D();
const dummyV = new Vector3();
const pointer = new Vector2();

const raycaster = new Raycaster();

let renderer: WebGLRenderer;
let scene: Scene;
let camera: PerspectiveCamera;
let imesh: InstancedMesh;
let controls: OrbitControls;
let effectComposer: EffectComposer;

const whirlpoolCanvasContainerRef = ref<HTMLDivElement>();
const whirlpoolCanvasRef = ref<HTMLCanvasElement>();

loadParticleInstances();

function loadParticleInstances() {
  for (let i = 0; i < props.particleCount; i++) {
    instances.push({
      position: new Vector3(rndFS(200), rndFS(200), rndFS(200)),
      scale: rnd(0.1, 0.5),
      scaleZ: rnd(0.05, 0.3),
      velocity: new Vector3(rndFS(1), rndFS(1), rndFS(1)),
      attraction: 0.01 + rnd(-0.005, 0.005),
      vLimit: 0.6 + rnd(-0.05, 0.05),
    });
  }
}

function setupScene() {
  if (!whirlpoolCanvasRef.value) {
    throw new Error("Canvas not initialized");
  }

  const width = whirlpoolCanvasRef.value.clientWidth;
  const height = whirlpoolCanvasRef.value.clientHeight;

  renderer = new WebGLRenderer({
    canvas: whirlpoolCanvasRef.value,
    antialias: true,
    alpha: true,
  });
  renderer.setSize(width, height);
  renderer.setClearColor(0x000000, 0);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.autoClear = true;

  camera = new PerspectiveCamera();
  camera.aspect = width / height;
  camera.position.set(0, 0, 200);
  camera.updateProjectionMatrix();

  const ambientLight = new AmbientLight(0x808080, 0.3);

  const boxGeometry = new BoxGeometry(2, 2, 10);
  const standardMaterial = new MeshBasicMaterial({
    transparent: true,
    opacity: 0.5,
    color: 0xffffff,
  });

  imesh = new InstancedMesh(boxGeometry, standardMaterial, props.particleCount);

  scene = new Scene();
  scene.add(ambientLight);
  scene.add(imesh);

  controls = new OrbitControls(camera, renderer.domElement);

  effectComposer = new EffectComposer(renderer);
  effectComposer.setSize(width, height);
  effectComposer.addPass(new RenderPass(scene, camera));

  const unrealBloomPass = new UnrealBloomPass(new Vector2(width, height), 0.3, 0.1, 0);
  effectComposer.addPass(unrealBloomPass);
}

function init() {
  for (let i = 0; i < props.particleCount; i++) {
    const inst = instances[i]!;
    dummyO.position.copy(inst.position);
    dummyO.scale.set(inst.scale, inst.scale, inst.scaleZ);
    dummyO.updateMatrix();
    imesh.setMatrixAt(i, dummyO.matrix);
  }

  const colors = new Float32Array(props.particleCount * 3);

  for (let i = 0; i < props.particleCount; i++) {
    // All white particles
    colors[i * 3] = 1;
    colors[i * 3 + 1] = 1;
    colors[i * 3 + 2] = 1;
  }

  imesh.instanceColor = new InstancedBufferAttribute(colors, 3);
  imesh.instanceMatrix.needsUpdate = true;
}

function animate() {
  requestAnimationFrame(animate);
  controls.update();

  for (let i = 0; i < props.particleCount; i++) {
    const inst = instances[i]!;
    const { position, scale, scaleZ, velocity, attraction, vLimit } = inst;

    dummyV.copy(target).sub(position).normalize().multiplyScalar(attraction);

    velocity.add(dummyV).clampScalar(-vLimit, vLimit);
    position.add(velocity);

    dummyO.position.copy(position);
    dummyO.scale.set(scale, scale, scaleZ);
    dummyO.lookAt(dummyV.copy(position).add(velocity));
    dummyO.updateMatrix();
    imesh.setMatrixAt(i, dummyO.matrix);
  }
  imesh.instanceMatrix.needsUpdate = true;

  effectComposer.render();
}

onMounted(() => {
  setupScene();
  init();
  animate();

  document.addEventListener("mousemove", onPointerMove);
  document.addEventListener("touchmove", onPointerMove);
  window.addEventListener("resize", onWindowResize);
});

function onPointerMove(event: MouseEvent | TouchEvent) {
  if (!renderer || !camera) return;

  let clientX: number;
  let clientY: number;

  if (event instanceof TouchEvent) {
    const touch = event.touches[0];
    if (!touch) return;
    clientX = touch.clientX;
    clientY = touch.clientY;
  } else {
    clientX = (event as MouseEvent).clientX;
    clientY = (event as MouseEvent).clientY;
  }

  const rect = whirlpoolCanvasContainerRef.value!.getBoundingClientRect();
  const x = clientX - rect.left;
  const y = clientY - rect.top;

  if (x >= 0 && x <= rect.width && y >= 0 && y <= rect.height) {
    pointer.x = (x / rect.width) * 2 - 1;
    pointer.y = -(y / rect.height) * 2 + 1;

    raycaster.setFromCamera(pointer, camera);
    const planeZ = new Plane(new Vector3(0, 0, 1), 0);
    const point = new Vector3();
    raycaster.ray.intersectPlane(planeZ, point);
    target.copy(point);
  } else {
    target.set(0, 0, 0);
  }
}

function onWindowResize() {
  if (!whirlpoolCanvasRef.value || !renderer || !camera || !effectComposer) return;

  const width = whirlpoolCanvasContainerRef.value!.clientWidth;
  const height = whirlpoolCanvasContainerRef.value!.clientHeight;

  camera.aspect = width / height;
  camera.updateProjectionMatrix();

  renderer.setSize(width, height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  effectComposer.setSize(width, height);
}

onUnmounted(() => {
  document.removeEventListener("mousemove", onPointerMove);
  document.removeEventListener("touchmove", onPointerMove);
  window.removeEventListener("resize", onWindowResize);
});
</script>
