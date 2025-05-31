<template>
  <div id="app"></div>
</template>

<script>
export default {
  name: "App",
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

body {
  background-image: url("/background/background1.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

#app {
  min-height: 100vh;
}
</style>
