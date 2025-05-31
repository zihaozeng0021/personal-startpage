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

#app {
  min-height: 100vh;
}
</style>
