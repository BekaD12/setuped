<script setup>
import he from 'he'
const setupList = ref([])
const isLoading = ref(true)
const isModalOpen = ref(false)
const selectedSetup = ref(null)

const toggleScrollLock = (lock) => {
  document.body.style.overflow = lock ? 'hidden' : ''
}

const openModal = (setup) => {
  isModalOpen.value = true
  selectedSetup.value = setup
  toggleScrollLock(true)
}

const closeModal = () => {
  isModalOpen.value = false
  selectedSetup.value = null
  toggleScrollLock(false)
}

const getPostCreationTime = (createdUtc) => {
  const postDate = new Date(createdUtc * 1000)
  const currentDate = new Date()
  const timeDifferenceInSeconds = Math.floor((currentDate - postDate) / 1000)

  const hours = Math.floor(timeDifferenceInSeconds / 3600)
  const minutes = Math.floor((timeDifferenceInSeconds % 3600) / 60)

  const timeAgo = hours > 0
    ? `${hours} ${hours === 1 ? 'hour' : 'hours'} ago`
    : (minutes > 0
        ? `${minutes} ${minutes === 1 ? 'minute' : 'minutes'} ago`
        : 'Less than a minute ago')

  return { timeAgo }
}

onMounted(async () => {
  const response = await fetch('https://www.reddit.com/r/battlestations/hot/.json?t=week&limit=100')
  const data = await response.json()
  setupList.value = data.data.children
    .filter(child => child.data.post_hint === 'image')
    .map(child => ({
      id: child.data.id,
      title: he.decode(child.data.title),
      author: child.data.author,
      description: child.data.selftext,
      upvote: child.data.ups,
      createdUtc: child.data.created_utc,
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
            <a class="battlestations__item-link" @click="openModal(setup)">
              <img
                :src="setup.image" alt="Battlestation image" title="Go to the Reddit image link"
                class="battlestations__item-image" loading="lazy"
              >
            </a>
          </div>
          <div class="battlestations__text-container">
            <span class="battlestations__item-title">
              {{ setup.title }}
            </span>
            <span class="battlestations__item-upvotes">
              <div i="carbon-arrow-shift-down" /> {{ setup.upvote }} Upvotes
            </span>
          </div>
        </div>
      </template>
    </div>
  </div>

  <div v-if="isModalOpen" class="battlestations__modal" :class="{ open: isModalOpen }" @click="closeModal">
    <div class="battlestations__modal-container" @click.stop>
      <div i="carbon-close" class="battlestations__modal-close" @click="closeModal" />
      <div class="battlestations__modal-content">
        <img :src="selectedSetup.image" alt="Battlestation image" class="modal-image">
        <div class="modal-details">
          <span class="battlestations__modal-title">{{ selectedSetup.title }}</span>
          <div class="battlestations__modal-text">
            <span class="battlestations__modal-author">u/{{ selectedSetup.author }}</span>
            <span class="battlestations__modal-date">{{ getPostCreationTime(selectedSetup.createdUtc).timeAgo }}</span>
          </div>
          <p v-if="selectedSetup.description" class="battlestations__modal-desc">
            {{ selectedSetup.description }}
          </p>
          <a :href="selectedSetup.permalink" target="_blank" class="battlestations__modal-link btn">Open on Reddit
            <div i="carbon-launch" />
          </a>
        </div>
      </div>
    </div>
  </div>
  <div v-if="isModalOpen" class="modal-overlay" @click="closeModal" />
</template>

<style>
@import '~/styles/modal.css';
</style>
