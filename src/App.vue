<template>
  <div class="app">
    <header>
      <h1>Text to Speech</h1>
    </header>
    <main>
      <TTSInput />
    </main>
  </div>
</template>

<script setup>
import TTSInput from './components/TTSInput.vue'
</script>

<style>
.app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
  font-weight: normal;
}

header {
  text-align: center;
  margin-bottom: 2rem;
}

h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

@media (min-width: 1024px) {
  .app {
    padding: 2rem 6rem;
  }
}
</style>
