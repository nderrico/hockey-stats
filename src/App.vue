<script setup>
import ShotTracker from './components/ShotTracker.vue'
</script>

<template>
  <div class="app-container">
    <ShotTracker />
  </div>
</template>

<style>
.app-container {
  margin: 0;
  padding: 0;
  min-height: 100vh;
  width: 100vw;
  max-width: 100%;
  overflow-x: hidden;
}

/* Reset any default margins/padding */
html, body, #app {
  margin: 0;
  padding: 0;
  width: 100%;
  max-width: 100%;
  overflow-x: hidden;
}
</style>
