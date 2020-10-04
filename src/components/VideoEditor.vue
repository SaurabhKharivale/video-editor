<template>
  <button @click="play">Play animation</button>
  <button @click="add">Add to canvas</button>
  <input v-model="seconds" placeholder="Time in seconds" />
  <button @click="startRecording">start recording</button>
  <button @click="stopRecording">stop recording</button>
  <div ref="loader" class="animation"></div>
</template>

<script>
import * as PIXI from "pixi.js";
import bodymovin from "lottie-web";
import animation from "../assets/animations/redy-bee.json";
// import image from '../assets/animations/image.png'

export default {
  name: "VideoEditor",
  data() {
    return {
      app: null,
      stage: null,
      seconds: 5,
      moving: false,
      capturer: null
    };
  },
  mounted() {
    this.app = new PIXI.Application({
      backgroundColor: "0xffffff"
    });
    this.stage = new PIXI.Container();
    document.body.appendChild(this.app.view);
  },
  methods: {
    // add() {
    //   // let texture = PIXI.Texture.from(image);
    //   let item = PIXI.Sprite.from(image);
    //
    //   // center the sprite anchor point
    //   item.anchor.x = 0;
    //   item.anchor.y = 0;
    //
    //   // move the sprite to the center of the canvas
    //   item.position.x = 200;
    //   item.position.y = 200;
    //
    //   this.stage.addChild(item);
    //
    //   this.render();
    // },
    add() {
      const image = "img/image.png";

      const img = new PIXI.Sprite.from(image);

      img.anchor.x = 0.5;
      img.anchor.y = 0.5;

      img.position.x = 30;
      img.position.y = 200;

      img.interactive = true;
      img.on("mousedown", event => {
        console.log("mousedown");
        console.log(event);
        this.moving = true;
      });

      img.on("mousemove", event => {
        if (!this.moving) {
          return;
        }
        console.log("mousemove");
        console.log(event);
        img.position.x = event.data.global.x;
        img.position.y = event.data.global.y;
      });

      img.on("mouseup", event => {
        console.log("mouseup");
        console.log(event);
        this.moving = false;
      });
      //
      // img.on('mouseover', event => {
      //   console.log(event)
      //   console.log('clicked')
      //   console.log(img)
      // })

      this.app.stage.addChild(img);
    },
    // setup() {
    //
    //   const image = 'img/image.png'
    //   //
    //   // const avatar = new PIXI.Sprite.from(image);
    //   //
    //   // this.app.stage.addChild(avatar);
    //   // const img = new PIXI.Sprite(PIXI.loader.resources[image].texture)
    //   //
    //   // this.app.stage.addChild(img)
    //
    //   this.app.loader.add(image).load((loader, resources) => {
    //       // This creates a texture from a 'bunny.png' image
    //     console.log(resources)
    //     console.log(loader)
    //       const img = new PIXI.Sprite(resources.image);
    //
    //       // Setup the position of the image
    //       img.x = this.app.renderer.width / 2;
    //       img.y = this.app.renderer.height / 2;
    //
    //       // Rotate around the center
    //       img.anchor.x = 0.5;
    //       img.anchor.y = 0.5;
    //
    //       // Add the img to the scene we are building
    //       this.app.stage.addChild(img);
    //
    //       // Listen for frame updates
    //       this.app.ticker.add(() => {
    //            // each frame we spin the img around a bit
    //           img.rotation += 0.01;
    //       });
    //
    //       // this.render()
    //   });
    // },
    render() {
      requestAnimationFrame(this.render);

      this.app.render(this.stage);

      if (this.capturer) this.capturer.capture(this.app.view);
    },
    play() {
      const loader = this.$refs.loader;
      this.loadBMAnimation(loader);
    },
    loadBMAnimation(loader) {
      bodymovin.loadAnimation({
        container: loader,
        renderer: "svg",
        loop: true,
        autoplay: true,
        animationData: animation
      });
    },
    initRecording() {
      let options = {
        /* Recording options */
        format: "webm",
        framerate: "30FPS",
        start: function() {
          this.startRecording();
        },
        stop: function() {
          this.stopRecording();
        }
      };

      // eslint-disable-next-line no-undef
      this.capturer = new CCapture({
        verbose: true,
        display: false,
        framerate: parseInt(options.framerate),
        motionBlurFrames: 0,
        quality: 100,
        format: options.format,
        workersPath: "dist/src/",
        timeLimit: 0,
        frameLimit: 0,
        autoSaveTime: 0
      });
    },
    startRecording() {
      this.render();
      this.initRecording();
      this.capturer.start();
    },
    stopRecording() {
      this.capturer.stop();
      this.capturer.save();
    }
  }
};
</script>

<style>
.animation {
  height: 300px;
  width: 300px;
}

canvas {
  border: 1px solid #eee;
  box-shadow: 5px 5px 15px #eee;
}
</style>
