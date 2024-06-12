<template>
  <div>
    <hr>
    <h1>
      Câmera
    </h1>
    <div>
      <video ref="video" width="80%" height="80%" autoplay muted>
      </video>
      <br>
      <button @click="requestCameraPermission">Iniciar Câmera
      </button>
      <br>

      <p>
        Match Result: {{ matchResult }}</p>

      <button @click="detectFace">Detector de Face</button>
    </div>

  </div>
</template>

<script>
import * as  faceapi from 'face-api.js'
export default {
  data() {
    return {
      input: "./input.jpg",
      matchResult: null,
    }
  },
  methods: {
    async requestCameraPermission() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        this.$refs.video.srcObject = stream;
        this.cameraStream = stream;
        this.error = null;
      } catch (error) {
        if (error.name === "NotAllowdError") {
          this.error = "Erro ao acessar a câmera: " + error.massage;
        }
      }
    },
    detectFace() {
      const detection = faceapi.detectSingleFace(this.input
      ).withFaceLandmarks().withFaceDescriptor()

      if (detection) {
        const bestMatch = faceMatcher.findBestMatch(detection.descriptor)
        this.matchResult = bestMatch.toString()
        console.log(this.matchResult)
      }
    }
  }
}
</script>

<style scoped>
video {
  transform: scaleX(-1);
}
</style>