<script setup lang="ts">
const router = useRouter()
const { t } = useI18n()
</script>

<template>
  <div class="container-404">
    <span>{{ t('not-found') }}</span>
    <button btn @click="router.back()">
      {{ t('button.back') }}
    </button>
  </div>
</template>

<style scoped>
.container-404 {
  padding: 4rem;
  text-align: center;
}

span {
  margin-right: 1rem;
}
</style>

<route lang="yaml">
meta:
  layout: 404
</route>
