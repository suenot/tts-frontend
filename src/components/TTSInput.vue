<template>
  <div class="tts-container">
    <div v-if="error" class="error-message">
      {{ error }}
    </div>
    <div class="voice-selector">
      <label>
        <input
          type="radio"
          v-model="voice"
          value="female"
          name="voice"
        > Женский голос
      </label>
      <label>
        <input
          type="radio"
          v-model="voice"
          value="male"
          name="voice"
        > Мужской голос
      </label>
    </div>
    <textarea
      v-model="text"
      placeholder="Введите текст на русском или английском языке. Каждое предложение будет озвучено на соответствующем языке."
      rows="4"
      class="input-area"
    ></textarea>
    
    <button 
      @click="handleSubmit"
      :disabled="isLoading || !text.trim()"
      class="submit-button"
    >
      {{ isLoading ? 'Генерация...' : 'Озвучить' }}
    </button>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from 'vue'

const text = ref('')
const voice = ref('female')
const isLoading = ref(false)
const error = ref('')
let audio = null

const handleSubmit = async () => {
  if (!text.value.trim()) return
  
  error.value = ''
  isLoading.value = true
  try {
    const response = await fetch('http://localhost:8000/api/tts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'audio/wav'
      },
      body: JSON.stringify({ 
        text: text.value,
        voice: voice.value
      })
    })
    
    if (!response.ok) {
      const data = await response.json()
      throw new Error(data.detail || 'Произошла ошибка при генерации речи')
    }
    
    const blob = await response.blob()
    if (audio) {
      audio.pause()
      audio = null
    }
    
    audio = new Audio(URL.createObjectURL(blob))
    await audio.play()
  } catch (error) {
    console.error('Error:', error)
    error.value = error.message
  } finally {
    isLoading.value = false
  }
}

onUnmounted(() => {
  if (audio) {
    audio.pause()
    audio = null
  }
})
</script>

<style scoped>
.tts-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding: 1rem;
}

.voice-selector {
  display: flex;
  gap: 1.5rem;
  margin-bottom: 0.5rem;
}

.voice-selector label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
}

.input-area {
  width: 100%;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
  resize: vertical;
  background: #fff;
  color: #333;
}

.submit-button {
  padding: 0.8rem 1.5rem;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s;
}

.submit-button:hover:not(:disabled) {
  background-color: #45a049;
}

.submit-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.error-message {
  color: #dc3545;
  background-color: #f8d7da;
  border: 1px solid #f5c6cb;
  padding: 0.75rem 1.25rem;
  margin-bottom: 1rem;
  border-radius: 4px;
}
</style>
