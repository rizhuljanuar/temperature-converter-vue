<script setup>
import TemperatureConverter from './components/TemperatureConverter.vue';

  
</script>

<template>
  <div class="app-container">
    <TemperatureConverter />
  </div>
</template>

<style scoped>
.app-container {
  max-width: 600px;
  margin: 50px auto;
  text-align: center;
  font-family: Arial, sans-serif;
}

h1 {
  color: #42b983;
  margin-bottom: 10px;
}

p {
  color: #666;
  font-size: 1.1em;
}
</style>
