<!-- src/App.vue -->
<template>
  <div id="app">
    <TimeCard />
    <Background />
    <SearchBar />
    <Portfolio />
  </div>
</template>

<script>
import Background from "./components/Background.vue";
import TimeCard from "./components/TimeCard.vue";
import SearchBar from "./components/SearchBar.vue";
import Portfolio from "./components/Portfolio.vue";

export default {
  name: "App",
  components: {
    Background,
    TimeCard,
    SearchBar,
    Portfolio,
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

:root {
  font-family: system-ui, Avenir, Helvetica, Arial, sans-serif;
  line-height: 1.5;
  font-weight: 400;

  color-scheme: light dark;
  color: rgba(255, 255, 255, 0.87);
  background-color: #242424;

  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  min-height: 100vh;
}

@media (prefers-color-scheme: light) {
  :root {
    color: #213547;
    background-color: #ffffff;
  }
  a:hover {
    color: #747bff;
  }
  button {
    background-color: #f9f9f9;
  }
}

@media (max-width: 480px) {
  #app {
    padding: 1rem;
  }
}
</style>
