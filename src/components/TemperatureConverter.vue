<script setup>
import { ref } from 'vue';

const title = "Temperature Interactive";
const celsius = ref(25);
const history = ref([]);

function getTemperatureClass(temp) {
  if (temp >= 35) return 'hot';
  if (temp >= 20) return 'warm';
  if (temp >= 10) return 'cool';
  return 'cold';
}

function addToHistory(action) {
  history.value.push({
    temperature: celsius.value,
    action: action,
    time: new Date().toLocaleTimeString()
  });
}

const tempClass = getTemperatureClass(celsius.value);

function increase() {
  celsius.value += 5;
  const newClass = getTemperatureClass(celsius.value);
  
  addToHistory('increase');
}

function decrease() {
  celsius.value -= 5;
  const newClass = getTemperatureClass(celsius.value);
  addToHistory('decrease');
}

function reset() {
  celsius.value = 25;
  addToHistory('reset');
}
</script>

<template>
  <div class="converter">
    <h1>{{ title }}</h1>

    <p :class="tempClass">{{ celsius }}</p>

    <div class="buttons">
      <button @click="decrease">Cold -5</button>
      <button @click="reset">Reset</button>
      <button @click="increase">Hot +5</button>
    </div>

    <div class="info">
      <p v-if="celsius >= 35">Sangat panas!</p>
      <p v-else-if="celsius >= 20">Nyaman</p>
      <p v-else>Dingin!</p>
    </div>

    <div class="history">
      <h3>History Perubahan:</h3> 
      <ul>
        <li v-for="(item, index) in history" :key="index">
          {{ item.time }} - {{ item.action }} -> {{ item.temperature }}°C
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.converter {
  padding: 30px;
  border-radius: 10px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  max-width: 500px;
  margin: 0 auto;
}

.hot {
  color: #ff6b6b;
  font-size: 3em;
  font-weight: bold;
  text-shadow: 2px 2px rgba(255, 0, 0, 0.3);
}

.warm {
  color: #ffa94d;
  font-size: 2.5em;
  font-weight: bold;
}

.cool {
  color: #51cf66;
  font-size: 2em;
}

.buttons {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin: 20px 0;
}

button {
  padding: 10px 20px;
  font-size: 1em;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background: white;
  color: #667eea;
  font-weight: bold;
  transition: transform 0.1s;
}

button:hover {
  transform: scale(1.05);
}

button:active {
  transform: scale(0.95);
}

.info {
  margin-top: 20px;
  padding: 15px;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 8px;
}

.info p {
  font-size: 1.2em;
  margin: 0;
}

.history {
  margin-top: 20px;
  padding: 15px;
  background: rgba(255, 255, 255, 0.3);
  border-radius: 8px;
  max-height: 200px;
  overflow-y: auto;
}

.history h3 {
  margin-top: 0;
}

.history ul {
  list-style: none;
  padding: 0;
}

.history li {
  padding: 8px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  font-size: 0.9em;
}
</style>
