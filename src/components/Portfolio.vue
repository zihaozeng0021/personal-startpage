<template>
  <div
      class="portfolio-entry"
      @click="showAlert"
  >
    <p class="entry-text">Portfolio | 访客入口</p>
  </div>
</template>

<script setup>

function showAlert() {
  alert('portfolio is not yet completed')
}
</script>

<style scoped>
.portfolio-entry {
  position: fixed;
  top: 50%;
  left: 15%;
  background-color: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  padding: 12px 20px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  cursor: pointer;
  user-select: none;
  transition: transform 0.1s ease-in-out, background-color 0.2s;
}

.portfolio-entry:hover {
  background-color: rgba(255, 255, 255, 0.3);
  transform: translateY(-2px);
}

.entry-text {
  margin: 0;
  font-size: 1rem;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}
</style>
