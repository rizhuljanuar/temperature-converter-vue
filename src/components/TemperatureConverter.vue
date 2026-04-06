<script setup>
import { ref, computed } from 'vue'

// Input states
const celsius = ref(25)
const fahrenheitInput = ref(77)
const kelvinInput = ref(298.15)

// Computed: Konversi dari masing-masing input
const fromCelsius = computed(() => {
  return {
    fahrenheit: (celsius.value * 9/5) + 32,
    kelvin: celsius.value + 273.15
  }
})

const fromFahrenheit = computed(() => {
  return {
    celsius: (fahrenheitInput.value - 32) * 5/9,
    kelvin: (fahrenheitInput.value - 32) * 5/9 + 273.15
  }
})

const fromKelvin = computed(() => {
  return {
    celsius: kelvinInput.value - 273.15,
    fahrenheit: (kelvinInput.value - 273.15) * 9/5 + 32
  }
})

// Temperature category
const category = computed(() => {
  const temp = celsius.value;

  if (temp >= 40) return { 
    text: 'Extreme Heat', 
    class: 'extreme-hot',
    description: 'Bahaya! suhu sangat tinggi'
  }

  if (temp >= 35) return { 
    text: 'Very Hot', 
    class: 'very-hot',
    description: 'Suhu panas, hindari aktivitas berat'
  }

  if (temp >= 30) return { 
    text: 'Hot', 
    class: 'hot',
    description: 'Cuaca panas, minum banyak air'
  }

  if (temp >= 25) return { 
    text: 'Warm', 
    class: 'warm',
    description: 'Suhu nyaman untuk aktivitas'
  }

  if (temp >= 20) return {
    text: 'Comfortable',
    class: 'comfortable',
    description: 'Suhu ideal dan sejuk'
  }

  if (temp >= 15) return {
    text: 'Cool',
    class: 'cool',
    description: 'Mulai dingin, pakai jaket tipis'
  }

  if (temp >= 10) return {
    text: 'Cold',
    class: 'cold',
    description: 'Dingin, pakai jaket tebal'
  }

  if (temp >= 0) return {
    text: 'Freezing',
    class: 'freezing',
    description: 'Sangat dingin, hati-hati es'
  }

  return {
    text: 'Absolute zero zone',
    class: 'absolute-zero',
    description: 'Suhu di bawah titik beku!'
  }
});

const absoluteZeroC = -273.15;
const isValidCelsius = computed(() => {
  return celsius.value >= absoluteZeroC
})

const validationMessage = computed(() => {
  if (!isValidCelsius.value) {
    return `Suhu tidak boleh di bawah ${absoluteZeroC}°C (Absolute Zero)!`;
  }

  return 'Semua konversi valid';
});

// Update other inputs saat satu input berubah
function syncFromCelsius() {
  if (isValidCelsius.value) {
    fahrenheitInput.value = fromCelsius.value.fahrenheit;
    kelvinInput.value = fromCelsius.value.kelvin;
  }
}

function syncFromFahrenheit() {
  const newCelcius = fromFahrenheit.value.celsius;

  if (newCelcius >= absoluteZeroC) {
    celsius.value = newCelcius;
    kelvinInput.value = fromFahrenheit.value.kelvin;
  }
}

function syncFromKelvin() {
  const newCelcius = fromKelvin.value.celsius;

  if (newCelcius >= absoluteZeroC) {
    celsius.value = fromKelvin.value.celsius
    fahrenheitInput.value = fromKelvin.value.fahrenheit
  }
}

// Methods
function reset() {
  celsius.value = 25
  fahrenheitInput.value = 77
  kelvinInput.value = 298.15
}

function setTemperature(celsiusValue) {
  celsius.value = celsiusValue;
  syncFromCelsius();
}
</script>

<template>
  <div class="converter-app">
    <header class="app-header">
      <h1 class="app-title">Advanced Temperature Converter</h1>
      <p class="app-subtitle">Vue 3 + Composition API + Two-way binding</p>
    </header>
    
    <main class="converter-card">

      <!-- Input Section -->
      <section class="input-section">
        <h2 class="section-title">Input Temperature</h2>

        <div class="inputs-grid">

          <!-- Celsius Input -->
          <div class="input-group">
            <label for="celsius-input" class="input-label">
              <span class="label-icon">
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M4.463 19.538Q3 18.075 3 16q0-1.2.525-2.238T5 12V6q0-1.25.875-2.125T8 3t2.125.875T11 6v6q.95.725 1.475 1.763T13 16q0 2.075-1.463 3.538T8 21t-3.537-1.463M5 16h6q0-.725-.312-1.35T9.8 13.6L9 13V6q0-.425-.288-.712T8 5t-.712.288T7 6v7l-.8.6q-.575.425-.888 1.05T5 16m13.5 4V7.8l-1.1 1.1L16 7.5L19.5 4L23 7.5l-1.425 1.4L20.5 7.825V20z"/></svg>
              </span>
              Celsius
            </label>
            <div class="input-wrapper">
              <input 
                type="number"
                id="celsius-input"
                v-model.number="celsius"
                @input="syncFromCelsius"
                class="temp-input"
                :class="{ invalid: !isValidCelsius }"
                step="0.01"
                placeholder="0"
              />
              <span class="input-unit">°C</span>
            </div>
            <div class="conversion-preview">
              <p>{{ fromCelsius.fahrenheit.toFixed(2) }}</p>
              <p>{{ fromCelsius.kelvin.toFixed(2) }}</p>
            </div>
          </div>

          <!-- Fahrenheit Input -->
          <div class="input-group">
            <label for="fahrenheit-input" class="input-label">
              <span class="label-icon"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M7.9 20.875q-1.75-1.05-2.825-2.863Q4 16.2 4 14q0-2.825 1.675-5.425q1.675-2.6 4.6-4.55q.55-.375 1.138-.038Q12 4.325 12 5v1.3q0 .85.588 1.425q.587.575 1.437.575q.425 0 .813-.187q.387-.188.687-.538q.2-.25.513-.313q.312-.062.587.138Q18.2 8.525 19.1 10.275q.9 1.75.9 3.725q0 2.2-1.075 4.012q-1.075 1.813-2.825 2.863q.425-.6.663-1.313Q17 18.85 17 18.05q0-1-.375-1.887q-.375-.888-1.075-1.588L12 11.1l-3.525 3.475q-.725.725-1.1 1.6Q7 17.05 7 18.05q0 .8.238 1.512q.237.713.662 1.313ZM12 21q-1.25 0-2.125-.863Q9 19.275 9 18.05q0-.575.225-1.112q.225-.538.65-.963L12 13.9l2.125 2.075q.425.425.65.95q.225.525.225 1.125q0 1.225-.875 2.087Q13.25 21 12 21Z"/></svg></span>
              Fahrenheit
            </label>
            <div class="input-wrapper">
              <input 
                type="number"
                id="fahrenheit-input"
                v-model.number="fahrenheitInput"
                @input="syncFromFahrenheit"
                class="temp-input"
                step="0.01"
                placeholder="32"
              />
              <span class="span-unit">°F</span>
            </div>
            <div class="conversion-preview">
              <p>{{ fromFahrenheit.celsius.toFixed(2) }}°C<</p>
              <p>{{ fromFahrenheit.kelvin.toFixed(2) }}K</p>
            </div>
          </div>

          <!-- Kelvin Input -->
          <div class="input-group">
            <label for="kelvin-input" class="input-label">
              <span class="label-icon"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M20 10q-.425 0-.712-.288T19 9t.288-.712T20 8t.713.288T21 9t-.288.713T20 10M10 22v-3.6L7.4 21L6 19.6l4-4V14H8.4l-4 4L3 16.6L5.6 14H2v-2h3.6L3 9.4L4.4 8l4 4H10v-1.6l-4-4L7.4 5L10 7.6V4h2v3.6L14.6 5L16 6.4l-4 4V12h8v2h-3.6l2.6 2.6l-1.4 1.4l-4-4H12v1.6l4 4l-1.4 1.4l-2.6-2.6V22zm9-15V2h2v5z"/></svg></span>
              Kelvin
            </label>
            <div class="input-wrapper">
              <input 
                type="number"
                id="kelvin-input"
                v-model.number="kelvinInput"
                @input="syncFromKelvin"
                class="temp-input"
                step="0.01"
                placeholder="273.15"
              />
              <span class="span-unit">°F</span>
            </div>
            <div class="conversion-preview">
              <p>{{ fromKelvin.celsius.toFixed(2) }}°C</p>
              <p>{{ fromKelvin.fahrenheit.toFixed(2) }}°F</p>
            </div>
          </div>
        </div>

        <div class="validation-message" :class="{ errpr: !isValidCelsius }">
          {{ validationMessage }}
        </div>
      </section>

      <!-- Category Display -->
      <section :class="['category-section', category.class]">
        <div class="category-icon">{{ category.text.split(' ')[0] }}</div>
        <h2 class="category-title">{{ category.text.substring(2) }}</h2>
        <p class="category-description">{{ category.description }}</p>

        <!-- Temperature Bar -->
        <div class="temperature-bar">
          <div
            class="bar-fill"
            :style="{ width: Math.min((celsius + 273.15) / 373.15 * 100, 100) + '%' }"
          ></div>
        </div>
        <p class="current-temp">{{ celsius.toFixed(1) }}°C</p>
      </section>

      <!-- Quick Actions -->
      <section class="actions-section">
        <h2 class="section-title">Quick Actions</h2>

        <div class="preset-buttons">
          <button
            @click="setTemperature(0)"
            class="preset-btn freezing-preset"
            title="Titik beku air"
          >
            Freezing
            <span>0°C</span>
          </button>

          <button
            @click="setTemperature(25)"
            class="preset-btn room-preset"
            title="Suhu ruangan"
          >
            Room
            <span>25°C</span>
          </button>

          <button
            @click="setTemperature(37)"
            class="preset-btn body-preset"
            title="Suhu tubuh manusia"
          >
            Body
            <span>37°C</span>
          </button>

          <button
            @click="setTemperature(100)"
            class="preset-btn boiling-preset"
            title="Titik didih air"
          >
            Boiling
            <span>100°C</span>
          </button>
        </div>

        <div class="action-buttons">
          <button @click="reset" class="action-btn reset-btn">
            Reset
          </button>
        </div>
      </section>
    </main>

    <!-- Info Section -->
    <footer class="info-section">
      <h3>Temperature Facts</h3>
      <ul class="facts-list">
        <li>
          <strong>Absolute Zero:</strong> -273.15°C - Suhu terdingin secara teoritis
        </li>
        <li>
          <strong>Freezing Point:</strong> 0°C - Titik beku air
        </li>
        <li>
          <strong>Room Temperature:</strong> 20-25°C - Suhu ruangan nyaman
        </li>
        <li>
          <strong>Body Temperature:</strong> 37°C - Suhu normal tubuh manusia
        </li>
        <li>
          <strong>Boiling Point:</strong> 100°C - Titik didih air
        </li>
      </ul>
    </footer>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
   
}
.converter-app {
  min-height: 100vh;
  padding: 40px 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.app-header {
  text-align: center;
  margin-bottom: 40px;
  color: white;
}

.app-title {
  font-size: clamp(2em, 5vw, 3.5em);
  margin-bottom: 10px;
  text-shadow: 2px 2px 10px rgb(0, 0, 0, 0.3);
  animation: fadeInDown 0.8s ease;
}

.app-subtitle {
  font-size: 1.1em;
  opacity: 0.9;
  animation: fadeInUp 0.8s ease;
}

.converter-card {
  max-width: 1200px;
  margin: 0 auto;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: 40px;
  box-shadow: 0 20px 20px rgba(0, 0, 0, 0.4);
  backdrop-filter: blur(10px);
  animation: slideInUp 0.6s ease;
}

.section-title {
  font-size: 1.8em;
  margin-bottom: 25px;
  color: #333;
  display: flex;
  align-items: center;
  gap: 10px;
}

.input-section {
  margin-bottom: 40px;
}

.inputs-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 25px;
  margin-bottom: 20px;
}

.input-group {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  padding: 25px;
  border-radius: 15px;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  border: 2px solid transparent;
}

.input-group:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  border-color: #667eea;
}

.input-label {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 1.2em;
  font-weight: bold;
  color: #333;
  margin-bottom: 15px;
}

.label-icon {
  font-size: 1.5em;
}

.input-wrapper {
  position: relative;
  margin-bottom: 15px;
}

.temp-input {
  width: 100%;
  padding: 15px;
  padding-right: 50px;
  font-size: 1.5em;
  font-weight: bold;
  border: 3px solid #ddd;
  border-radius: 10px;
  background: white;
  color: #333;
  transition: all 0.3s ease;
}

.temp-input:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 20px rgba(102, 126, 234, 0.3);
  transform: scale(1.02);
}

.temp-input.invalid {
  border-color: #ff6b6b;
  background: #fff0f0;
  animation: shake 0.5s ease;
}

.input-unit {
  position: absolute;
  right: 15px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 1.3em;
  font-weight: bold;
  color: #667eea;
  pointer-events: none;
}

.conversion-preview {
  background: rgba(0, 0, 0, 0.1);
  padding: 12px;
  border-radius: 8px;
  font-size: 0.9em;
}

.conversion-preview p {
  margin: 5px 0;
  color: #555;
  font-weight: 500;
}

.validation-message {
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  font-weight: bold;
  font-size: 1.1em;
  background: #d4edda;
  color: #155724;
  transition: all 0.3s ease;
}

.validation-message.error {
  background: #f8d7da;
  color: #721c24;
  animation: pulse infinite;
}

.category-section {

  padding: 40px;
  border-radius: 15px;
  text-align: center;
  margin-bottom: 40px;
  color: white;
  transition: all 0.5s ease;
  animation: scaleIn 0.5s ease;
}

.category-icon {
  font-size: 4em;
  margin-bottom: 15px;
  animation: bounce 2s infinite;
}

.category-title {
  font-size: 2.5em;
  margin-bottom: 15px;
  font-weight: bold;
}

.category-description {
  font-size: 1.3em;
  opacity: 0.9;
  margin-bottom: 25px;

}


/* Temperature Bar */
.temperature-bar {
  width: 100%;
  height: 30px;

  background: rgba(255,255,255,0.3);
  border-radius: 15px;
  overflow: hidden;
  margin-bottom: 15px;
  box-shadow: inset 0 2px 10px rgba(0,0,0,0.2);
}

.bar-fill {
  height: 100%;
  background: linear-gradient(90deg, #4ecdc4, #ffe66d, #ff6b6b);
  border-radius: 15px;
  transition: width 0.5s ease;
  box-shadow: 0 0 20px rgba(255,255,255,0.5);
}

.current-temp {
  font-size: 2em;
  font-weight: bold;
  text-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}

/* Category Themes */
.category-section.extreme-hot {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  box-shadow: 0 15px 40px rgba(255, 107, 107, 0.5);
}


.category-section.very-hot {
  background: linear-gradient(135deg, #ffa94d 0%, #ff922b 100%);
  box-shadow: 0 15px 40px rgba(255, 169, 77, 0.5);
}

.category-section.hot {

  background: linear-gradient(135deg, #ffd43b 0%, #fab005 100%);
  box-shadow: 0 15px 40px rgba(255, 212, 59, 0.5);
  color: #333;
}

.category-section.warm {
  background: linear-gradient(135deg, #51cf66 0%, #40c057 100%);
  box-shadow: 0 15px 40px rgba(81, 207, 102, 0.5);
}

.category-section.comfortable {
  background: linear-gradient(135deg, #4ecdc4 0%, #44a08d 100%);
  box-shadow: 0 15px 40px rgba(78, 205, 196, 0.5);
}

.category-section.cool {
  background: linear-gradient(135deg, #4dabf7 0%, #339af0 100%);
  box-shadow: 0 15px 40px rgba(77, 171, 247, 0.5);
}

.category-section.cold {
  background: linear-gradient(135deg, #748ffc 0%, #5c7cfa 100%);
  box-shadow: 0 15px 40px rgba(116, 143, 252, 0.5);
}

.category-section.freezing {
  background: linear-gradient(135deg, #91a7ff 0%, #748ffc 100%);

  box-shadow: 0 15px 40px rgba(145, 167, 255, 0.5);
}

.category-section.absolute-zero {
  background: linear-gradient(135deg, #e599f7 0%, #cc5de8 100%);
  box-shadow: 0 15px 40px rgba(229, 153, 247, 0.5);
  animation: freeze 3s infinite;
}

.actions-section {
  margin-bottom: 30px;
}

.preset-buttons {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 15px;
  margin-bottom: 20px;
}

.preset-btn {
  padding: 20px 15px;
  font-size: 1em;
  font-weight: bold;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.preset-btn:hover {
  transform: scale(1.08);
  box-shadow: 0 8px 25px rgba(0,0,0,0.3);
}

.preset-btn:active {
  transform: scale(0.95);
}

.preset-btn span {
  font-size: 0.9em;
  font-weight: normal;

  opacity: 0.8;
}

.freezing-preset {
  background: linear-gradient(135deg, #4ecdc4 0%, #44a08d 100%);
  color: white;
}

.room-preset {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.body-preset {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
  color: white;
}

.boiling-preset {
  background: linear-gradient(135deg, #fa709a 0%, #fee140 100%);
  color: white;
}

.action-buttons {
  display: flex;
  justify-content: center;
  gap: 15px;
}

.action-btn {
  padding: 15px 40px;
  font-size: 1.1em;
  font-weight: bold;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
}

.action-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 20px rgba(0,0,0,0.3);
}

.action-btn:active {
  transform: scale(0.95);
}

.reset-btn {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  color: white;
}

.info-section {
  max-width: 1200px;
  margin: 40px auto 0;
  background: rgba(255,255,255,0.9);
  border-radius: 15px;
  padding: 30px;
  color: #333;
  animation: fadeIn 1s ease;
}

.info-section h3 {
  font-size: 1.8em;
  margin-bottom: 20px;
  color: #667eea;
}

.facts-list {
  list-style: none;
  padding: 0;
}

.facts-list li {
  padding: 15px;
  margin-bottom: 10px;
  background: rgba(102, 126, 234, 0.1);
  border-radius: 8px;
  border-left: 4px solid #667eea;
  transition: all 0.3s ease;
}

.facts-list li:hover {
  background: rgba(102, 126, 234, 0.2);
  transform: translateX(10px);
}

.facts-list li strong {
  color: #667eea;
}

@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;

    transform: translateY(30px);
  }
  to {

    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }

}

@keyframes scaleIn {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes bounce {
  0%, 100% {

    transform: translateY(0);
  }
  50% {
    transform: translateY(-15px);
  }
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);

  }
  50% {
    transform: scale(1.02);
  }
}


@keyframes shake {
  0%, 100% {
    transform: translateX(0);
  }
  25% {
    transform: translateX(-10px);
  }
  75% {
    transform: translateX(10px);
  }
}

@keyframes freeze {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.7;
  }

}

@media (max-width: 768px) {
  .converter-app {
    padding: 20px 10px;
  }
  
  .app-title {
    font-size: 2em;
  }
  
  .converter-card {
    padding: 25px;
  }
  
  .section-title {
    font-size: 1.4em;
  }
  
  .inputs-grid {
    grid-template-columns: 1fr;
  }
  
  .preset-buttons {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .category-title {
    font-size: 1.8em;
  }
  
  .category-description {
    font-size: 1.1em;
  }
}

@media (max-width: 480px) {
  .preset-buttons {
    grid-template-columns: 1fr;
  }
  
  .temp-input {
    font-size: 1.2em;
  }
  
  .category-icon {
    font-size: 3em;
  }
}
</style>
