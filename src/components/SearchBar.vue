<!-- src/components/SearchBar.vue -->
<template>
  <div class="search-bar-container">
    <input
        v-model="query"
        @keyup.enter="performSearch"
        class="search-input"
        type="text"
        placeholder="Search Google..."
    />
    <button class="search-button" @click="performSearch">
      Search
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const query = ref('');

function performSearch() {
  const trimmed = query.value.trim();
  if (!trimmed) return;
  const url = `https://www.google.com/search?q=${encodeURIComponent(trimmed)}`;
  window.open(url, '_blank');
}
</script>

<style scoped>
.search-bar-container {
  position: absolute;
  left: 50%;
  top: 40%;
  transform: translate(-50%, -50%);
  width: 600px;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  background-color: rgba(255, 255, 255, 0.2);
  padding: 12px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
  backdrop-clip: padding-box;
}

.search-input {
  flex: 1;
  padding: 8px 12px;
  font-size: 1rem;
  border: 1px solid rgba(255, 255, 255, 0.6);
  border-radius: 6px;
  background: rgba(255, 255, 255, 0.8);
  color: #000;
  outline: none;
  margin-right: 8px;
  transition: border-color 0.2s;
}

.search-input:focus {
  border-color: rgba(0, 0, 0, 0.8);
}

.search-button {
  padding: 8px 16px;
  font-size: 1rem;
  font-weight: bold;
  border: none;
  border-radius: 6px;
  background-color: rgba(0, 123, 255, 0.9);
  color: #fff;
  cursor: pointer;
  transition: background-color 0.2s;
}

.search-button:hover {
  background-color: rgba(0, 123, 255, 1);
}
</style>
