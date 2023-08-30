<script setup>
import he from 'he'
const setupList = ref([])
const isLoading = ref(true)

onMounted(async () => {
  const response = await fetch('https://www.reddit.com/r/battlestations/hot/.json?t=week&limit=100')
  const data = await response.json()
  setupList.value = data.data.children
    .filter(child => child.data.post_hint === 'image')
    .map(child => ({
      id: child.data.id,
      title: he.decode(child.data.title),
      upvote: child.data.ups,
      subreddit: child.data.subreddit,
      url: child.data.url,
      image: child.data.preview?.images[0]?.resolutions[3]?.url?.replace(/&amp;/g, '&') || child.data.thumbnail,
      permalink: `https://www.reddit.com${child.data.permalink}`,
    }))
  isLoading.value = false
})
</script>

<template>
  <div class="battlestations">
    <h1 class="battlestations__title">
      Setuped
    </h1>
    <span class="battlestations__subtitle text-gradient">Hot posts from r/battlestations subreddit</span>

    <div class="battlestations__list">
      <template v-if="isLoading">
        <PostSkeleton v-for="index in 12" :key="index" />
      </template>

      <template v-else>
        <div v-for="setup in setupList" :key="setup.id" class="battlestations__item">
          <div class="battlestations__item-image-container">
            <a :href="setup.url" target="_blank" class="battlestations__item-link">
              <img :src="setup.image" alt="Battlestation image" title="Go to the Reddit image link" class="battlestations__item-image" loading="lazy">
            </a>
          </div>
          <div class="battlestations__text-container">
            <a :href="setup.permalink" target="_blank" class="battlestations__item-title" title="Go to Reddit post link">
              {{ setup.title }}
            </a>
            <span class="battlestations__item-upvotes">{{ setup.upvote }} Upvotes</span>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>
