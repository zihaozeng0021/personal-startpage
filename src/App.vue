<!-- src/App.vue -->
<template>
  <div id="app">
    <Background />
  </div>
</template>

<script>
import Background from "./components/Background.vue";

export default {
  name: "App",
  components: {
    Background,
  },
};
</script>

<style>

html,
body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

#app {
  min-height: 100vh;
}
</style>
