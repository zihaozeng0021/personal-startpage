<!-- src/components/Background.vue -->
<template>
  <div class="background"></div>
</template>

<script>
export default {
  name: "Background",
};
</script>

<style>
.background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url("/background/background1.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  z-index: -1;
}
</style>
