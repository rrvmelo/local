<template>
  <div>
    <hr>
    <h1>
      Câmera
    </h1>
    <div>
      <video ref="video" width="640" height="360" autoplay muted>
      </video>
      <button @click="startVideo">Iniciar Vídeo</button>

      <br>

    </div>

  </div>
</template>

<script>
// Importa a biblioteca face-api.js
import * as faceapi from 'face-api.js';
import { ref, onMounted } from 'vue';

export default {
  data() {
    return {
      videoVisible: false,
      // Variável para armazenar a imagem de referência
      referenceImage: null,
      similaridade: null,

    };
  },
  methods: {
    async startVideo() {
      // Obtém referências para os elementos de vídeo e canvas
      const videoRef = this.$refs.video;
      //const canvasEl = this.$refs.canvas;

      // Solicita permissão para usar a câmera se ainda não estiver solicitada
      if (!this.videoVisible) {
        try {
          // Obtém a permissão para acessar a câmera
          const stream = await navigator.mediaDevices.getUserMedia({ video: true });
          // Define o stream de vídeo no elemento de vídeo
          videoRef.srcObject = stream;
          // Define a visibilidade do vídeo e do canvas como verdadeira
          this.videoVisible = true;
        } catch (error) {
          console.error('Erro ao acessar a câmera:', error);
        }
      }

    },
     async loadModels() {
      
      try {
       await faceapi.nets.faceRecognitionNet.loadFromUri('/public/models')
       await faceapi.nets.faceLandmark68Net.loadFromUri('/public/models')
       await faceapi.nets.ssdMobilenetv1.loadFromUri('/public/models')
      } catch (err) {
        console.error('Erro ao carregar modelos: ', err)
      }
     },
    async detectFaces() {
      if (videoRef.value.readyState === videoRef.value.HAVE_VIDEO) {
        const detections = await faceapi
          .detectAllFaces(videoRef.value)
          .withFaceLandmarks()
          .withFaceDescriptors()

        const displaySize = { width: videoRef.value.videoWidth, height: videoRef.value.videoHeight }
        const resizedDetections = faceapi.resizeResults(detections, displaySize)
        const canvas = faceapi.createCanvasFromMedia(videoRef.value)
        document.body.append(canvas)
        faceapi.matchDimensions(canvas, displaySize)

        const results = resizedDetections.map((d) => faceMatcher.findBestMatch(d.descriptor))
        const context = canvas.getContext('2d')
        context.clearRect(0, 0, canvas.width, canvas.height)

        results.forEach((result, i) => {
          const box = resizedDetections[i].detection.box
          const drawBox = new faceapi.draw.DrawBox(box, { label: result.toString() })
          drawBox.draw(canvas)
        })

        requestAnimationFrame(detectFaces)
      } else {
        requestAnimationFrame(detectFaces)
      }
    }

    // onMounted(startVideo)
    // return { videoRef, }
  }
}

</script>
<style scoped>
video {
  transform: scaleX(-1);
}

canvas {
  position: absolute;
}
</style>