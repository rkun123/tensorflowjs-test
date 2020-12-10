<template>
  <div class="container">
    <video class="webcamVideo" autoplay></video>
    <canvas class="overlayCanvas" width="640" height="480"></canvas>
  </div>
</template>

<script>
import * as facelandmark from '@tensorflow-models/face-landmarks-detection'
import * as tf from '@tensorflow/tfjs-core'
//import * as tfjsWasm from '@tensorflow/tfjs-backend-webgl'
import '@tensorflow/tfjs-backend-webgl'
import { drawMesh } from '../utilities'



export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted: async function() {
    //tfjsWasm.setWasmPath('/tfjs-backend-wasm.wasm')
    async function estimate(video) {
      const model = await facelandmark.load(facelandmark.SupportedPackages.mediapipeFacemesh)
      const predictions = await model.estimateFaces({ input: video})
      ctx.clearRect(0, 0, canvas.width, canvas.height)
      //drawBox(predictions, ctx)
      drawMesh(predictions, ctx)
      console.log(predictions)
    }

    const video = document.querySelector("video")
    const canvas = document.querySelector(".overlayCanvas")
    const ctx = canvas.getContext("2d")
    console.log(ctx)
    if(navigator.mediaDevices.getUserMedia) {
      navigator.mediaDevices.getUserMedia({video: true}).then(stream => {
        video.srcObject = stream
      })
      tf.setBackend('webgl').then(() => {
        console.log("Loaded")
        setInterval(estimate, 35, video)
      })
    }
    //await estimate(video)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container {
  position: absolute;
}
.webcamVideo {
  position: absolute;
}
.overlayCanvas {
  position: absolute;
}
</style>
