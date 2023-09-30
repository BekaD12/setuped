<script setup lang="ts">
useHead({
  title: 'Setuped',
  meta: [
    { name: 'description', content: 'Explore the hottest setup posts from Reddit and get inspired to enhance your own setup' },
    {
      name: 'theme-color',
      content: computed(() => isDark.value ? '#121212' : '#edf1F5'),
    },
  ],
  link: [
    {
      rel: 'icon',
      type: 'image/svg+xml',
      href: computed(() => preferredDark.value ? '/favicon-dark.svg' : '/favicon.svg'),
    },
  ],
})
</script>

<template>
  <RouterView />
</template>
