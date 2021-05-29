<template>
  <div id="space">
    <div id="container"></div>
  </div>
</template>

<script>
import * as THREE from "three";
import { MapControls } from "three/examples/jsm/controls/OrbitControls";

export default {
  name: "space",
  //relacionado a global data
  data() {
    return {
      //threejs variáveis
      scene: null,
      camera: null,
      renderer: null,
      controls: null,

      iR: null,
    };
  },
  mounted() {
    let that = this;

    this.Awake();

    //função resize que atualiza o tamanho da tela
    window.addEventListener("resize", onWindowResize, false);

    function onWindowResize() {
      that.camera.aspect = window.innerWidth / window.innerHeight;
      that.camera.updateProjectionMatrix();
      that.renderer.setSize(window.innerWidth, window.innerHeight);
    }

    onWindowResize();
  },
  methods: {
    Awake() {
      let container = document.getElementById("container");

      //inicializa cena
      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color(0x222222);

      //inicializa câmera
      this.camera = new THREE.PerspectiveCamera(
        25,
        window.clientWidth / window.clientHeight,
        1,
        100
      );
      //posição(x,y,z)
      this.camera.position.set(8, 4, 0);

      //inicializa grupo de objetos
      this.iR = new THREE.Group();
      this.iR = "Interactive Root";
      this.scene.add(this.iR);

      //inicializa luzes
      let light0 = new THREE.AmbientLight(0xfafafa, 0.25);

      let light1 = new THREE.PointLight(0xfafafa, 0.4);
      light1.position.set(200, 90, 40);

      let light2 = new THREE.PointLight(0xfafafa, 0.4);
      light2.position.set(200, 90, -40);

      this.scene.add(light0);
      this.scene.add(light1);
      this.scene.add(light2);

      //mostrar background com linhas de referência (tamanho, divisões, cor de linha central, cor das demais linhas)
      let gridHelper = new THREE.GridHelper(
        60,
        150,
        new THREE.Color(0x555555),
        new THREE.Color(0x333333)
      );
      this.scene.add(gridHelper);

      //adicionando uma mesh geometry para teste
      let geometry = new THREE.BoxGeometry(1, 1, 1);
      let material = new THREE.MeshPhongMaterial({ color: 0x00ff00 });
      let mesh = new THREE.Mesh(geometry, material);
      this.scene.add(mesh);

      //inicializar renderizador
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setPixelRatio(window.devicePixelRatio);
      //para apresentação em full screen: (caso contrário, usar o width e height do container)
      this.renderer.setSize(window.innerWidth, window.innerHeight);

      //direcionar resultados do renderer como canvas
      container.appendChild(this.renderer.domElement);

      //inicializar controles
      this.controls = new MapControls(this.camera, this.renderer.domElement);
      this.controls.enableDamping = true;
      this.controls.dampingFactor = 0.25;
      this.controls.screenSpacePanning = false;
      this.controls.maxDistance = 800;

      this.controls.update();

      this.Update();
      this.GetGeoJson();
    },

    //função "chama a si mesma"
    Update() {
      requestAnimationFrame(this.Update);

      this.renderer.render(this.scene, this.camera);
      this.controls.update();
    },

    GetGeoJson() {
      fetch("./assets/ufpe.geojson").then((res) => {
        res.json().then((data) => {
          console.log(data);
          //this.LoadBuildings(data)
        });
      });
    },
  },
};
</script>


<!-- scopped: limita css a apenas este componente-->
<style scoped>
</style>