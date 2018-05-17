<template lang="html">
  <div :class="'img-'+this.mainImgID" id="cvs_thumnails_box">
    <canvas id="cvs">
    </canvas>
    <div id="thumbnails">
      <div v-for="t in thumbnails" @click="changeImg(t.id)" :class="'thumbnail-'+ t.id" class="thumbnail">
      </div>
    </div>
  </div>
</template>
<script>
import {Container} from './../assets/javascripts/pixi.min.js'
import {WebGLRenderer} from './../assets/javascripts/pixi.min.js'
import {Sprite} from './../assets/javascripts/pixi.min.js'
import {Graphics} from './../assets/javascripts/pixi.min.js'
import {Filter} from './../assets/javascripts/pixi.min.js'
import {Matrix} from './../assets/javascripts/pixi.min.js'
export default {
  props: {
    'productId': Number
  },
  data: () => {
    return {
      mainImgID: 0,
      thumbnails: Array.apply(null, { length: 5 })
        .map(function (_, idx) {
          return {
            id: idx,
          }
        })
    }
  },
  mounted: function () {
    this.drawCanvas(this.mainImgID)
  },
  methods: {
    changeImg: function (imgID) {
      this.drawCanvas(imgID)
    },
    drawCanvas: function(imgID) {
      let cvs = document.getElementById('cvs')
      cvs.width = 800
      cvs.height = 540
      let renderer = new PIXI.WebGLRenderer(cvs.width, cvs.height, {backgroundColor : 0xffffff, view: cvs, antialias: true} )
      let container = new PIXI.Container()
      let img = new Image()
      img.src = "./../assets/images/" + imgID + ".png";
      img.onload = function(){
        let sprite = new PIXI.Sprite(new PIXI.Texture(new PIXI.BaseTexture(img)))
        sprite.width = cvs.width
        sprite.height = cvs.height
        container.addChild(sprite)
        renderer.render(container)
      }
      let frag =
        `precision mediump float;
        uniform vec4 filterArea;
        uniform sampler2D uSampler;
        uniform float val;
        varying vec2 vTextureCoord;
        void main (void) {
          vec4 col = texture2D(uSampler, vTextureCoord);
          if(vTextureCoord.x > val) gl_FragColor = texture2D(uSampler, vec2(val, vTextureCoord.y));
          else                      gl_FragColor = col;
        }`.split('\n').reduce((c, a) => c + a.trim() + '\n')
      let uniforms = {
        val: { type: 'float', value: 0 }
      };
      let filter = new PIXI.Filter(null, frag, uniforms);
      container.filters = [filter]
      let count = 0;
      function update(){
        count += Math.pow(0.15 * Math.random(), 2);
        uniforms = {
          val: { type: 'float', value: count },
          mappedMatrix : { type: 'mat3', value: new PIXI.Matrix() }
        };
        filter = new PIXI.Filter(null, frag, uniforms);
        container.filters = [filter]
        renderer.render(container)
        requestAnimationFrame(update)
      }
      update();
    }
  }
}
</script>

<style lang="scss" scoped>
$pc   : 1026px;
$ipad : 1025px;
$smp : 670px;
#cvs_thumnails_box {
  background-color: #f5f5f5;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}
#cvs{
  width: 48%;
  height: 100%;
  margin: 1%;
  // @media (max-width: $ipad){
  //   margin-bottom: 0px;
  //   margin-right: 4%;
  //   width: 50%;
  // }
  // @media (max-width: $smp){
  //   margin-bottom: 50px;
  //   width: 100%;
  // }
}
#thumbnails{
  width: 48%;
  height: 100%;
  margin: 1%;
  // @media (max-width: $ipad){
  //   width: 45%;
  //   height: 200px;
  // }
  // @media (max-width: $smp){
  //   width: 100%;
  //   height: 130px;
  // }
  display: flex;
  justify-content: left;
  flex-wrap: wrap;
  .thumbnail {
    //border: 1px solid #ddd;
    box-sizing: border-box;
    width: 33%;
    height: 198px;
    @media (max-width: $ipad){
      margin-top:0%;
      width: 120px;
      height: 120px;
    }
    @media (max-width: $smp){
      margin-top:0%;
      width: 23%;
      height: 60px;
    }
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    transition: 0.3s;
    &:hover {
      filter: blur(3px) brightness(0.9)
    }
    @for $i from 0 through 4 {
      &.thumbnail-#{$i} {
        background-image: url('./../assets/images/#{$i}.png');
      }
    }
  }
}
</style>