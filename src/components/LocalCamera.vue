<template>
  <div>
    <h1>Localização e Camera</h1>

    <!--Se tiver erro sai aqui-->
    <div v-if="error">{{ error }}</div>

    <!--Vai que vai-->
    <div v-else>
      <div>
        <p>Latitude da PMH: {{ latpmh }}</p>
        <p>Longitude da PMH: {{ lonpmh }}</p>
        <p>Latitude: {{ latitude }}</p>
        <p>Longitude: {{ longitude }}</p>
        <p>Distância: {{ distance.toFixed(2) }} </p>
        <p>Menor que 300 metros: {{ MenorQue }}</p>
        <button @click="RequestLocationPermission">
          Obter Localização
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      latpmh: -22.8761639,
      lonpmh: -47.1836421,
      latitude: null,
      longitude: null,
      //latitude: ,
      //longitude: ,
      distance: 0,
      MenorQue: false,
      error: null,
    };
  },
  methods: {
    // Pede permissão e busca a localização do usuário
    RequestLocationPermission() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          this.showPosition,
          this.showError,
        );
      } else {
        this.error = "Deu erro.";
      }
    },
    // Mostra a informação de Lat e Lon
    showPosition(position) {
      this.latitude = position.coords.latitude;
      this.longitude = position.coords.longitude;
      this.error = null;

      // Calcula a distância e armazena na variável `distance`
      this.distance = this.haversineDistance(this.latpmh, this.lonpmh, this.latitude, this.longitude);
      
      // Verifica se a distância é menor que 300 metros e armazena o resultado 
      this.MenorQue = this.distance < 300;
    },
    //Haversine
    haversineDistance(latpmh, lonpmh, latitude, longitude) {
      //Função para converter graus em radianos
      const radianos = (degree) => degree * (Math.PI / 180);
      //Radio da Terra
      const raio = 6371000;

      const diferencaLat = radianos(latpmh - latitude);
      const diferencaLon = radianos(lonpmh - longitude);

      const formulaA =
        Math.sin(diferencaLat / 2) * Math.sin(diferencaLat / 2) +
        Math.cos(radianos(latpmh)) * Math.cos(radianos(latitude)) *
        Math.sin(diferencaLon / 2) * Math.sin(diferencaLon / 2);

      const formulaC = 2 * Math.atan2(Math.sqrt(formulaA), Math.sqrt(1 - formulaA));

      return raio * formulaC;
    },

    // Possiveis erros
    showError(error) {
      switch (error.code) {
        case error.PERMISSION_DENIED:
          this.error = "Usuário negou a solicitação de Geolocalização.";
          break;
        case error.POSITION_UNAVAILABLE:
          this.error = "Informações de localização não estão disponíveis.";
          break;
        case error.TIMEOUT:
          this.error = "A solicitação para obter a localização do usuário expirou.";
          break;
        case error.UNKNOWN_ERROR:
          this.error = "Ocorreu um erro desconhecido.";
          break;
      }
    },
  },
}
</script>
