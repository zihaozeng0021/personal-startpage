<!-- src/components/Portfolio.vue -->
<template>
  <div class="footer-links">
    <a
        href="https://zihaozeng0021.github.io/personal-portfolio/"
        target="_blank"
        rel="noopener noreferrer"
    >About the Author</a
    >
    <a
        href="https://github.com/zihaozeng0021/personal-startpage"
        target="_blank"
        rel="noopener noreferrer"
    >Code for the Project</a
    >
    <a
        href="https://github.com/zihaozeng0021"
        target="_blank"
        rel="noopener noreferrer"
    >My GitHub</a
    >
    <a href="mailto:zihaozeng0021@gmail.com">Send me Emails</a>
  </div>
</template>

<script setup>
</script>

<style scoped>
.footer-links {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 1rem;
  /* Make background slightly translucent */
  background-color: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  padding: 0.5rem 1rem;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  user-select: none;
  transition: transform 0.1s ease-in-out, background-color 0.2s;
  width: 90%;
  max-width: 800px;
  justify-content: center;
}

.footer-links a {
  font-size: 0.85rem;
  color: #fff;
  text-decoration: none;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

.footer-links a:hover {
  text-decoration: underline;
}

@media (max-width: 480px) {
  .footer-links {
    flex-direction: column;
    gap: 0.5rem;
    bottom: 10px;
    padding: 0.5rem;
  }
  .footer-links a {
    font-size: 0.8rem;
  }
}
</style>

