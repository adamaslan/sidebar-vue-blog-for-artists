<template>
  <div ref="canvasHolder" class="canvasHolder">
    <div ref="canvas" class="canvas"></div>
  </div>
</template>

<script>
import * as THREE from 'three'
import { Clock, ACESFilmicToneMapping } from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

export default {
  components: {},
  props: {},
  data() {
    return {
      camera: null,
      cameraHolder: null,
      scene: null,
      world: null,
      renderer: null,
      container: null,
      controls: null,
      sunLight: null,
      ambientLight: null,
      txt_loader: null,
      gltf_loader: null,
      clock: null,
      enableOrbitControls: true, // dev
    }
  },
  computed: {},
  watch: {},
  mounted() {
    const self = this
    if (process.browser) {
      self.$nextTick(function () {
        self.init()
      })
    }
  },
  methods: {
    init() {
      const self = this
      // setup
      self.container = self.$refs.canvas
      self.scene = new THREE.Scene()
      self.scene.background = new THREE.Color(0x9acae6)
      self.stagecenter = new THREE.Vector3(0, 0, 0)
      self.lastCameraPos = new THREE.Vector3(0, 0, 0)

      // setup renderer
      self.clock = new Clock()
      self.renderer = new THREE.WebGLRenderer({
        alpha: true,
        antialias: true,
        physicallyCorrectLights: true,
        powerPreference: 'high-performance',
      })
      self.renderer.setSize(
        self.container.clientWidth,
        self.container.clientHeight
      )
      self.renderer.shadowMap.enabled = true // important!
      self.renderer.shadowMap.type = THREE.PCFSoftShadowMap
      self.renderer.toneMapping = THREE.ACESFilmicToneMapping
      self.renderer.toneMappingExposure = 1
      self.renderer.outputEncoding = THREE.sRGBEncoding
      self.renderer.setPixelRatio(window.devicePixelRatio)
      self.container.appendChild(self.renderer.domElement)
      // self.renderer.gammaFactor = 2.2

      // setup camera
      self.camera = new THREE.PerspectiveCamera(
        40,
        self.container.clientWidth / self.container.clientHeight,
        0.1,
        500
      )
      if (self.enableOrbitControls) {
        self.camera.position.set(0, 1.5, 0)
        self.camera.rotation.set(0, 0, 0)
      } else {
        self.camera.position.set(0, 0, 0)
        self.cameraHolder = new THREE.Group()
        self.cameraHolder.add(self.camera)
        self.cameraHolder.position.set(0, 1.25, 3.5)
        self.camera.rotation.set(-0.16, 0, 0)
        self.scene.add(self.cameraHolder)
        // self.scene.fog = new THREE.Fog(0x9acae6, 0.0025, 35)
      }

      // orbit controls cam
      if (self.enableOrbitControls) {
        self.controls = new OrbitControls(
          self.camera,
          self.renderer.domElement
        )
        self.controls.enableDamping = true
        self.controls.dampingFactor = 0.1
        self.camera.position.set(0, 1.5, 0)
        self.camera.rotation.set(0, 0, 0)
        // self.controls.target.set(0, 1.5, 0)
        // self.controls.update()
      }

      // create world
      self.createWorld()

      // add resize listener
      window.addEventListener('resize', self.resize, false)
    },
    setupLightShadow() {
      const self = this
      const shadowSize = 20
      // sun light
      self.sunLight = new THREE.DirectionalLight(0xffffff, 1.0)
      self.sunLight.castShadow = true
      self.sunLight.shadow.camera.top = shadowSize
      self.sunLight.shadow.camera.bottom = -shadowSize
      self.sunLight.shadow.camera.left = -shadowSize
      self.sunLight.shadow.camera.right = shadowSize
      self.sunLight.shadow.mapSize.width = 2048 * 2
      self.sunLight.shadow.mapSize.height = 2048 * 2
      self.sunLight.shadow.bias = -0.000001
      self.sunLight.shadow.camera.near = 0.01
      self.sunLight.shadow.camera.far = 100
      self.sunLight.position.set(5, 10, -10)
      self.world.add(self.sunLight)
      // ambient light
      self.ambientLight = new THREE.AmbientLight(0xffffff, 0.4)
      self.world.add(self.ambientLight)
    },
    async createWorld() {
      const self = this
      // create world
      self.world = new THREE.Group()
      self.scene.add(self.world)
      self.world.position.set(0, 0, 0)
      await self.setupLightShadow()

      // add floor
      const floorGeom = new THREE.PlaneGeometry(2000, 2000)
      floorGeom.rotateX(-Math.PI / 2)
      const color = new THREE.Color(0x5eaa9b)
      color.convertSRGBToLinear()
      const floorMa = new THREE.MeshStandardMaterial({
        color,
        side: THREE.DoubleSide,
      })
      const floorPlane = new THREE.Mesh(floorGeom, floorMa)
      floorPlane.position.set(0, 0, 0)
      floorPlane.receiveShadow = true
      self.world.add(floorPlane)

      // add cube
      const geometry = new THREE.BoxBufferGeometry(1, 1, 1)
      const material = new THREE.MeshPhongMaterial({ color: 0x482b75 })
      const cube = new THREE.Mesh(geometry, material)
      cube.position.set(0, 0.501, 0)
      cube.rotation.set(0, 0, 0)
      cube.castShadow = true
      self.world.add(cube)

      // dev grid
      const size = 1000
      const divisions = 1000
      const gridHelper = new THREE.GridHelper(size, divisions)
      // self.world.add(gridHelper)

      // start render
      await self.renderer.setAnimationLoop(self.render.bind(self))
      self.resize()

      // hide loading here
      self.$root.$emit('finished-loading')
    },
    render(timestamp, frame) {
      const self = this
      // update orbit controls if enabled
      if (self.enableOrbitControls) {
        self.controls.update()
      }

      // update camera
      self.camera.aspect =
        self.container.clientWidth / self.container.clientHeight
      self.camera.updateProjectionMatrix()

      // render scene
      self.renderer.render(self.scene, self.camera)

      // render loop
      self.renderer.setAnimationLoop(self.render.bind(self))
    },
    resize() {
      const self = this
      // update camera
      self.camera.aspect =
        self.container.clientWidth / self.container.clientHeight
      self.camera.updateProjectionMatrix()
      self.renderer.setSize(
        self.container.clientWidth,
        self.container.clientHeight
      )
    },
  },
}
</script>

<style >
.canvasHolder,
.canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 80%;
  pointer-events: all;
  z-index: 1;
}
</style>
