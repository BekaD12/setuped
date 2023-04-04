<template>
  <div class="battlestations">
    <h1 class="battlestations__title">Setuped</h1>
    <span class="battlestations__subtitle">Hot posts of setup from Reddit</span>

    <div class="text-center">
      <button class="btn" @click="toggleSubreddit('battlestations')">Toggle Battlestations</button>
      <button class="btn" @click="toggleSubreddit('gamingsetups')">Toggle Gaming Setups</button>
      <button class="btn" @click="toggleSubreddit('macsetups')">Toggle Mac Setups</button>
    </div>

    <div v-if="posts.length" class="battlestations__list">
      <div v-for="post in filteredVisiblePosts" :key="post.id" class="battlestations__item">
        <a :href="'https://reddit.com' + post.permalink" class="battlestations__item-link" target="_blank">
          <img v-if="isValidImage(post.url)" :src="post.url" alt="Post image" class="battlestations__item-image"
            loading="lazy">
          <div v-else class="battlestations__item-no-image">No image available</div>
        </a>
        <div>
          <h2 class="battlestations__item-title">{{ post.title }}</h2>
          <p class="battlestations__item-upvotes">Upvotes: {{ post.ups }}</p>
          <span class="battlestations__item-subreddit">subreddit : {{ post.subreddit }}</span>
        </div>
      </div>
    </div>
    <div v-if="isLoading" class="battlestations__list battlestations__loading">
      <PostSkeleton v-for="index in 8" :key="index" />
    </div>
    <div v-if="!isLoading && !posts.length" class="battlestations__error">
      Failed to load posts. Please try again later.
    </div>
    <div ref="endOfList" class="battlestations__end-of-list"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';

const posts = ref([]);
const isLoading = ref(true);
const error = ref(null);
const visiblePosts = ref([]);
const hiddenSubreddits = ref([]);


const subreddits = ['battlestations', 'gamingsetups', 'macsetups'];
const url = `https://www.reddit.com/r/${subreddits.join('+')}/hot.json?limit=100`;

const isValidImage = (url) => {
  return url && url.includes('.jpg');
};

const loadMore = () => {
  const start = visiblePosts.value.length;
  const end = start + 8;
  visiblePosts.value = [...visiblePosts.value, ...posts.value.slice(start, end)];
};

const handleScroll = () => {
  const endOfList = document.querySelector('.battlestations__end-of-list');
  const endOfListPosition = endOfList.getBoundingClientRect().top + window.scrollY;
  if (window.innerHeight + window.scrollY >= endOfListPosition) {
    loadMore();
  }
};

const toggleSubreddit = (subreddit) => {
  hiddenSubreddits.value = hiddenSubreddits.value.includes(subreddit)
    ? hiddenSubreddits.value.filter((s) => s !== subreddit)
    : [...hiddenSubreddits.value, subreddit];
};

const filteredVisiblePosts = computed(() =>
  visiblePosts.value.filter((post) => !hiddenSubreddits.value.includes(post.subreddit))
);

onMounted(async () => {
  try {
    const response = await fetch(url);
    const data = await response.json();
    const children = data.data.children;
    for (let i = 0; i < children.length; i++) {
      const post = children[i].data;
      if (isValidImage(post.url)) {
        posts.value.push(post);
      }
    }
    visiblePosts.value = posts.value.slice(0, 8);
    isLoading.value = false;

    window.addEventListener('scroll', handleScroll);
  } catch (error) {
    console.error(error);
    isLoading.value = false;
    error.value = error.message;
  }
});
</script>

<route lang="yaml">
meta:
  layout: home
</route>
