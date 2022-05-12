<script setup lang="ts">
useHead({
  meta: [
    { property: 'og:title', content: 'kkopite' },
    { property: 'og:image', content: 'https://kkopite.netlify.app/avatar.png' },
    { name: 'description', content: 'kkopite\'s Portfolio' },
    { name: 'twitter:card', content: 'summary' },
    { name: 'twitter:creator', content: '@kkopitehong' },
  ],
})
</script>

<template>
  <NavBar />
  <main class="px-7 py-10">
    <router-view />
    <Footer />
  </main>
</template>
