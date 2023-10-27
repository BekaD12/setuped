<script setup>
import he from 'he'
const setupList = ref([])
const isLoading = ref(true)
const isModalOpen = ref(false)
const selectedSetup = ref(null)

const lockBodyScroll = () => {
  document.body.style.overflow = 'hidden'
}

const unlockBodyScroll = () => {
  document.body.style.overflow = ''
}

const openModal = (setup) => {
  isModalOpen.value = true
  selectedSetup.value = setup
  lockBodyScroll()
}

const closeModal = () => {
  isModalOpen.value = false
  selectedSetup.value = null
  unlockBodyScroll()
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
              <div i="carbon-arrow-up" /> {{ setup.upvote }} Upvotes
            </span>
          </div>
        </div>
      </template>
    </div>
  </div>

  <div v-if="isModalOpen" class="battlestations__modal" :class="{ open: isModalOpen }" @click="closeModal">
    <div class="battlestations__modal-content" @click.stop>
      <div i="carbon-close" class="battlestations__modal-close" @click="closeModal" />
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
  <div v-if="isModalOpen" class="modal-overlay" @click="closeModal" />
</template>

<style scoped>
.battlestations__modal {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  z-index: 1000;
  transition: opacity 0.3s;
}

.battlestations__modal.open {
  display: block;
}

.battlestations__modal-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: var(--card-background);
  border-radius: var(--item-radius);
  box-shadow: var(--shadow);
  padding: 3.5rem;
}

.battlestations__modal-close {
  width: 2.5rem;
  cursor: pointer;
  height: 2.5rem;
  position: absolute;
  right: .75rem;
  top: .75rem;
  color: var(--button-color);
}

.battlestations__modal-close:active {
  background: rgba(255, 255, 255, 0.06);
}

.battlestations__modal-title {
  margin-top: 1rem;
  font-size: 1.8rem;
  font-weight: bold;
}

.battlestations__modal-text {
  margin: 1rem 0;
  display: flex;
  gap: .5rem;
  font-size: 1.6rem;
}

.battlestations__modal-desc {
  padding: 1.5rem 0;
  letter-spacing: .1rem;
  font-size: 1.5rem;
}

.battlestations__modal-link {
  display: flex;
  width: fit-content;
  padding: 1.5rem;
  align-items: center;
  gap: .5rem;
}

.battlestations__modal .carbon-launch {
  margin-top: -0.2rem;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: var(--overlay);
  z-index: 999;
}

@media (max-width: 576px) {
  .battlestations__modal-content {
    padding: 0;
    left: unset;
    top: 10%;
    transform: none;
    margin: auto 1rem;
  }

  .modal-details {
    padding: 1.5rem;
  }

  .battlestations__modal-desc {
    line-height: 1.2;
  }
}
</style>
