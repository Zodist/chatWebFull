<template>
  <v-layout align-center justify-center>
    <div id="myCanvas"></div>
  </v-layout>
</template>

<script>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { USDZExporter } from "three/examples/jsm/exporters/USDZExporter.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { RoomEnvironment } from "three/examples/jsm/environments/RoomEnvironment.js";
export default {
  data() {
    return {
      items: [],
    };
  },
  computed: {},
  created() {},
  mounted() {
    this.test2();
  },
  methods: {
    test2() {
      let camera, scene, renderer;
      renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setPixelRatio(window.devicePixelRatio);
      renderer.setSize(window.innerWidth, window.innerHeight);
      renderer.toneMapping = THREE.ACESFilmicToneMapping;
      renderer.outputEncoding = THREE.sRGBEncoding;
      // document.body.appendChild(renderer.domElement);
      var myCanvas = document.getElementById("myCanvas");
      myCanvas.appendChild(renderer.domElement);

      camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.25,
        20
      );
      camera.position.set(-2.5, 0.6, 3.0);

      const pmremGenerator = new THREE.PMREMGenerator(renderer);

      scene = new THREE.Scene();
      scene.background = new THREE.Color(0xf0f0f0);
      scene.environment = pmremGenerator.fromScene(
        new RoomEnvironment(),
        0.04
      ).texture;

      const loader = new GLTFLoader().setPath(
        "models/gltf/DamagedHelmet/glTF/"
      );
      // "/models/gltf/DamagedHelmet/glTF/DamagedHelmet.gltf"
      loader.load("DamagedHelmet.gltf", async function (gltf) {
        console.log("gltf:",gltf)
        scene.add(gltf.scene);

        // const shadowMesh = createSpotShadowMesh();
        // shadowMesh.position.y = -1.1;
        // shadowMesh.position.z = -0.25;
        // shadowMesh.scale.setScalar(2);
        // scene.add(shadowMesh);

        // render();
        renderer.render( scene, camera );

        // USDZ

        const exporter = new USDZExporter();
        const arraybuffer = await exporter.parse(gltf.scene);
        const blob = new Blob([arraybuffer], {
          type: "application/octet-stream",
        });

        const link = document.getElementById("link");
        link.href = URL.createObjectURL(blob);
      });

      const controls = new OrbitControls(camera, renderer.domElement);
      controls.addEventListener("change", ()=> {
        renderer.render( scene, camera );
      }); // use if there is no animation loop
      controls.minDistance = 2;
      controls.maxDistance = 10;
      controls.target.set(0, -0.15, -0.2);
      controls.update();

      // window.addEventListener("resize", onWindowResize);
    },
    test() {
      const scene = new THREE.Scene();
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );

      const renderer = new THREE.WebGLRenderer();
      renderer.setSize(window.innerWidth, window.innerHeight);

      var myCanvas = document.getElementById("myCanvas");
      myCanvas.appendChild(renderer.domElement);

      const geometry = new THREE.BoxGeometry();
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);

      camera.position.z = 5;

      const animate = function () {
        requestAnimationFrame(animate);

        cube.rotation.x += 0.01;
        cube.rotation.y += 0.01;

        renderer.render(scene, camera);
      };

      animate();
    },
  },
};
</script>

<style></style>