<template>
  <div>
    <hr>
    <h1>
      Câmera
    </h1>
    <!--Botões-->
    <button @click="requestCameraPermission">Iniciar Câmera</button>
    <video :srcObject.prop="stream" width="80%" height="80%" autoplay></video>
    <button @click="capture">Capturar Foto</button>
    <button @click="stopCamera">Parar Foto</button>
    <canvas ref="canvas" width="320" height="240" style="display: none;"></canvas>
  </div>
</template>

<script>


</script>