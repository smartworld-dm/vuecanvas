<template lang="html">
  <div>
    <button v-on:click="onClick()" style="width: 150px; height: 40px; margin-bottom: 20px;">Start</button>
    <div id="cvs">
    </div>
  </div>
</template>
<script>
import {Container, WebGLRenderer, Sprite, Graphics, Filter, Matrix} from './../assets/javascripts/pixi.min.js'

export default {
  props: {
    'productId': Number
  },
  data: () => {
    return {
      mainImgID: 0,
      // Container
      container: null,
      stage: null,
      // Mask
      bg: null,
      bg1: null,
      mask: null,
      maskScale: 0,
      // Renderer
      renderer: null,
      // Request
      requestId: '',
      // Counter
      counter: 0,
      // Update?
      isUpdate: false,
      // Frame
      fps: 20,
      now: 0,
      then: 0,
      interval: 0,
      delta: 0,
    }
  },
  mounted: function () {
    this.container = new PIXI.Container()
    this.stage = new PIXI.Container()
    this.renderer = new PIXI.autoDetectRenderer(800, 600, {antialias: true} )
    document.getElementById("cvs").appendChild(this.renderer.view)

    // Frame
    this.interval = 1000 / this.fps
    this.then = Date.now()
    
    this.drawCanvas()
  },
  methods: {
    onClick: function (){
      this.counter = 0
      this.isUpdate = true

      this.stage.removeChild(this.mask);
      this.container.mask = null;

      this.mask = PIXI.Sprite.fromImage('public/images/cloud-texture4.png')
      this.mask.anchor.x = 0.5
      this.mask.anchor.y = 0.5
      this.mask.position.x = this.renderer.width / 2
      this.mask.position.y = this.renderer.height / 2
      this.mask.width = 100
      this.mask.height = 100
      this.mask.alpha = 0.9
      this.mask.rotation = Math.floor(Math.random() * 11) 

      this.stage.addChild(this.mask)
      this.container.mask = this.mask

      this.animate()
    },
    animate: function (){
      if (this.counter > 20){
      console.log("counter - ", this.counter);
        this.isUpdate = false
        this.stage.removeChild(this.mask);
        this.renderer.render(this.stage)
      }

      if (this.isUpdate) requestAnimationFrame(this.animate)

      this.now = Date.now()
      this.delta = this.now - this.then
       
      if (this.delta > this.interval) {
        this.then = this.now - (this.delta % this.interval)

        this.mask.width += this.counter++ * 20
        this.mask.height += this.counter * 20
        this.renderer.render(this.stage)
      }
    },
    drawCanvas: function() {
      this.stage.interactive = true

      this.container.position.x = this.renderer.width / 2
      this.container.position.y = this.renderer.height / 2
      this.bg1 = PIXI.Sprite.fromImage('public/images/first.jpg')
      this.bg1.anchor.x = 0.5
      this.bg1.anchor.y = 0.5
      this.bg1.position.x = 0
      this.bg1.position.y = 0
      this.bg1.width = 800
      this.bg1.height = 600
      
      this.container.addChild(this.bg1)
      this.stage.addChild(this.container)
      this.renderer.render(this.stage)
    }
  }
}
</script>