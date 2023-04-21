<script setup>
const posts = ref([])
const isLoading = ref(true)
const error = ref(null)
const visiblePosts = ref([])
const hiddenSubreddits = ref([])

const subreddits = ['battlestations']
const url = `https://www.reddit.com/r/${subreddits.join('+')}/hot.json?limit=100`

const isValidImage = (url) => {
  return url && url.includes('.jpg')
}

const loadMore = () => {
  const start = visiblePosts.value.length
  const end = start + 9
  visiblePosts.value = [...visiblePosts.value, ...posts.value.slice(start, end)]
}

const handleScroll = () => {
  const endOfList = document.querySelector('.battlestations__end-of-list')
  const endOfListPosition = endOfList.getBoundingClientRect().top + window.scrollY
  if (window.innerHeight + window.scrollY >= endOfListPosition)
    loadMore()
}

const clearLocalStorage = () => {
  localStorage.clear()
}

onMounted(async () => {
  try {
    const cacheKey = 'setuped'
    const cachedData = localStorage.getItem(cacheKey)
    if (cachedData) {
      posts.value = JSON.parse(cachedData)
      visiblePosts.value = posts.value.slice(0, 8)
      isLoading.value = false
    }

    const response = await fetch(url)
    const data = await response.json()
    const children = data.data.children
    for (let i = 0; i < children.length; i++) {
      const post = children[i].data
      if (isValidImage(post.url))
        posts.value.push(post)
    }

    visiblePosts.value = posts.value.slice(0, 9)
    isLoading.value = false
    localStorage.setItem(cacheKey, JSON.stringify(posts.value))
    window.addEventListener('scroll', handleScroll)

    setInterval(clearLocalStorage, 900000)
  }
  catch (error) {
    console.error(error)
    isLoading.value = false
    error.value = error.message
  }
})
</script>

<template>
  <div class="battlestations">
    <h1 class="battlestations__title">
      Setuped
    </h1>
    <span class="battlestations__subtitle text-gradient">Hot posts of setup from Reddit</span>

    <div v-if="posts.length" class="battlestations__list">
      <div v-for="post in visiblePosts" :key="post.id" class="battlestations__item">
        <div class="battlestations__item-image-container">
          <a :href="`https://reddit.com${post.permalink}`" class="battlestations__item-link" target="_blank">
            <img
              v-if="isValidImage(post.url)" :src="post.url" alt="Post image"
              class="battlestations__item-image" loading="lazy"
            >
          </a>
        </div>
        <div class="battlestations__text-container">
          <h2 class="battlestations__item-title">
            {{ post.title }}
          </h2>
          <p class="battlestations__item-upvotes">
            Upvotes: {{ post.ups }}
          </p>
          <span class="battlestations__item-subreddit">subreddit : {{ post.subreddit }}</span>
        </div>
      </div>
    </div>
    <div v-if="isLoading" class="battlestations__list battlestations__loading">
      <PostSkeleton v-for="index in 8" :key="index" />
    </div>
    <div v-if="!isLoading && !posts.length" class="battlestations__error">
      Failed to load posts
    </div>
    <div ref="endOfList" class="battlestations__end-of-list" />
  </div>
</template>
