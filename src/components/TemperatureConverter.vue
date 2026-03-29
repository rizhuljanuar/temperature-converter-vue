<script setup>
import { computed, ref } from 'vue';

const celsius = ref(25);
const history = ref([]);

const fahrenheit = computed(() => {
  return (celsius.value * 9/5) + 32;
})

const kelvin = computed(() => {
  return celsius.value + 273.15;
})

const temperatureCategory = computed(() => {
  if (celsius.value >= 35) return 'Very hot';
  if (celsius.value >= 30) return 'Hot';
  if (celsius.value >= 20) return 'Warm';
  if (celsius.value >= 10) return 'Cool';
  return 'Cold';
});

function addToHistory(action) {
  history.value.push({
    temperature: celsius.value,
    action: action,
    time: new Date().toLocaleTimeString()
  });
}

const tempClass = computed(() => {
  if (celsius.value >= 30) return 'hot';
  if (celsius.value >= 20) return 'warm';
  if (celsius.value >= 10) return 'cool';
  return 'cold';
});

function increase() {
  celsius.value += 5;
  addToHistory('increase');
}

function decrease() {
  celsius.value -= 5;
  addToHistory('decrease');
}

function reset() {
  celsius.value = 25;
  addToHistory('reset');
}
</script>

<template>
  <div class="converter">
    <h1>Temperature Converter</h1>
    <p class="subtitle">Reactive conversion with Computed Properties</p>

    <div class="temperature-display">
      <div class="main-temp">
        <label for="celsius">Celsius (°C)</label>
        <input 
          type="text"
          id="celsius"
          v-model.number="celsius"
          class="temp-input"
        />
      </div>

      <div class="conversions">
        <div class="conversion-item">
          <span class="unit">Fahrenheit (°F)</span>
          <span class="value">{{ fahrenheit.toFixed(2) }}</span>
        </div>

        <div class="conversion-item">
          <span class="unit">Kelvin (K):</span>
          <span class="value">{{ kelvin.toFixed(2) }}</span>
        </div>
      </div>

      <div :class="['category', tempClass]">
        {{ temperatureCategory }}
      </div>

      <div class="controls">
        <button @click="decrease()" class="btn-decrease">-5 °C</button>
        <button @click="reset()" class="btn-reset">Reset</button>
        <button @click="increase()" class="btn-increase">+5 °C</button>
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
  </div>
</template>

<style scoped>
.converter {
  padding: 30px;
  border-radius: 10px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  max-width: 600px;
  margin: 0 auto;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
}

h1 {
  margin: 0 0 10px 0;
  font-size: 2.5em;
}

.subtitle {
  margin-bottom: 30px;
  opacity: 0.9;
  font-size: 1.1em;
}

.temperature-display {
  background: rgba(255, 255, 255, 0.1);
  padding: 30px;
  border-radius: 12px;
  backdrop-filter: blur(10px);
}

.main-temp {
  margin-bottom: 30px;
}

.main-temp label {
  display: block;
  margin-bottom: 10px;
  font-size: 1.2em;
  font-weight: bold;
}

.temp-input {
  width: 100%;
  padding: 15px;
  font-size: 1.5em;
  border: none;
  border-radius: 8px;
  text-align: center;
  background: white;
  color: #333;
  font-weight: bold;
}

.conversions {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin: 30px 0;
}

.conversion-item {
  background: rgba(255, 255, 255, 0.1);
  padding: 20px;
  border-radius: 8px;
  text-align: center;
}

.conversion-item .unit {
  display: block;
  font-size: 0.9em;
  opacity: 0.8;
  margin-bottom: 8px;
}

.conversion-item .value {
  display: block;
  font-size: 2em;
  font-weight: bold;
}

.category {
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  font-size: 1.5em;
  font-weight: bold;
  margin: 20px 0;
  transition: all 0.3s ease;
}

.category.hot {
  background: #ff6b6b;
  animation: pulse-hot 2s infinite;
}

.category.warm {
  background: #ffa94d;
}

.category.cool {
  background: #51cf66;
}

.category.cold {
  background: #4ecdc4;
  animation: shake 2s infinite;
}

@keyframes pulse-hot {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.controls {
  display: flex;
  gap: 15px;
  justify-content: center;
  margin-top: 30px;
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
