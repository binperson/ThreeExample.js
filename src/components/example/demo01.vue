<template>
  <div class="DDA">
    <canvas v-if="suportWebGL" ref="canvas" style="width: 100% height: 100%"></canvas>
    <div v-else>
      <slot>
        Your browser does not seem to support
        <a
          href="http://khronos.org/webgl/wiki/Getting_a_WebGL_Implementation"
          style="color:#000"
        >WebGL</a>.
        <br>'
      </slot>
    </div>
  </div>
</template>
<script type='text/ecmascript-6'>
import { OrbitControls } from "@/common/js/controls/OrbitControls";
import {
  Scene,
  PerspectiveCamera,
  WebGLRenderer,
  BufferGeometry,
  BufferAttribute,
  MeshBasicMaterial,
  Mesh,
  SpriteMaterial,
  Sprite,
  Texture,
  RepeatWrapping,
  Float32BufferAttribute,
  LineBasicMaterial,
  LineSegments,
  MeshPhongMaterial,
  SphereGeometry,
  FlatShading,
  SpotLight,
  Color
} from "three";

export default {
  data() {
    const suportWebGL = (() => {
      try {
        var canvas = document.createElement("canvas");
        return !!(
          window.WebGLRenderingContext &&
          (canvas.getContext("webgl") ||
            canvas.getContext("experimental-webgl"))
        );
      } catch (e) {
        return false;
      }
    })();
    return {
      scene: new Scene(),
      camera: new PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      ),
      renderer: null,
      suportWebGL,
      earth: null
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.controls = new OrbitControls(this.camera, this.$el);
      this.controls.type = "orbit";
      this.renderer = new WebGLRenderer({ canvas: this.$refs.canvas });
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.addLight();
      var geometry = new SphereGeometry(50, 20, 20, 0, Math.PI * 2, 0, Math.PI);
      //SphereGeometry(radius, widthSegments, heightSegments, phiStart, phiLength, thetaStart, thetaLength)

      /* Create Earth Sphere*/
      this.earth = new Mesh(geometry, this.Mat());
      this.scene.add(this.earth);
      // this.scene.remove(this.earth);

      this.camera.position.z = 90;
      console.log(this.scene);
      this.render();
    },
    addLight() {
      var spotLight = new SpotLight(0xffffff);
      spotLight.position.set(100, 100, 100);
      spotLight.castShadow = true; //If set to true light will cast dynamic shadows. Warning: This is expensive and requires tweaking to get shadows looking right.
      spotLight.shadowMapWidth = 1024;
      spotLight.shadowMapHeight = 1024;
      spotLight.shadowCameraNear = 500;
      spotLight.shadowCameraFar = 4000;
      spotLight.shadowCameraFov = 30;
      this.scene.add(spotLight);
    },
    Mat() {
      var material = new MeshPhongMaterial({
        color: new Color("rgb(35,35,213)"), //Diffuse color of the material
        emissive: new Color("rgb(64,128,255)"), //Emissive(light) color of the material, essentially a solid color unaffected by other lighting. Default is black.
        specular: new Color(
          "rgb(93,195,255)"
        ) /*Specular color of the material, i.e., how shiny the material is and the color of its shine. 
                                                          Setting this the same color as the diffuse value (times some intensity) makes the material more metallic-looking; 
                                                          setting this to some gray makes the material look more plastic. Default is dark gray.*/,
        shininess: 1, //How shiny the specular highlight is; a higher value gives a sharper highlight. Default is 30.
        shading: FlatShading, //How the triangles of a curved surface are rendered: THREE.SmoothShading, THREE.FlatShading, THREE.NoShading
        wireframe: 1, //THREE.Math.randInt(0,1)
        transparent: 1,
        opacity: 1 //THREE.Math.randFloat(0,1)
      });
      return material;
    },
    render() {
      requestAnimationFrame(this.render);
      this.earth.rotation.x += 0.01;
      this.earth.rotation.y += 0.01;
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>
<style scoped lang='stylus' rel='stylesheet/stylus'>
</style>
