<script setup lang="ts">
const { t } = useI18n()
</script>

<template>
  <nav>
    <button class="icon-btn mx-2 !outline-none" :title="t('button.toggle_dark')" @click="toggleDark()">
      <div i="carbon-sun dark:carbon-moon" />
    </button>
  </nav>
</template>

<style scoped>
nav {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding: 2rem 2rem 0 0;
}
</style>
