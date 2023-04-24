<script setup lang="ts">
const { t } = useI18n()
</script>

<template>
  <nav text-right p-4>
    <button class="icon-btn mx-2 !outline-none" :title="t('button.toggle_dark')" @click="toggleDark()">
      <div i="carbon-sun dark:carbon-moon" />
    </button>
  </nav>
</template>
