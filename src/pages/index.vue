<template>
  <div class="battlestations">
    <h1 class="battlestations__title">Top posts from r/battlestations:</h1>
    <div v-if="posts.length" class="battlestations__list">
      <div v-for="post in visiblePosts" :key="post.id" class="battlestations__item">
        <a :href="'https://reddit.com' + post.permalink" class="battlestations__item-link" target="_blank">
          <img v-if="isValidImage(post.url)" :src="post.url" alt="Post image" class="battlestations__item-image"
            loading="lazy">
          <div v-else class="battlestations__item-no-image">No image available</div>
        </a>
        <div>
          <h2 class="battlestations__item-title">{{ post.title }}</h2>
          <p class="battlestations__item-upvotes">Upvotes: {{ post.ups }}</p>
        </div>
      </div>
    </div>
    <div v-if="isLoading" class="battlestations__loading">
      <PostSkeleton v-for="index in 10" :key="index" />
    </div>
    <div v-if="!isLoading && !posts.length" class="battlestations__error">
      Failed to load posts. Please try again later.
    </div>
    <div ref="endOfList" class="battlestations__end-of-list"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const posts = ref([]);
const isLoading = ref(true);
const error = ref(null);
const visiblePosts = ref([]);

const loadMore = () => {
  const start = visiblePosts.value.length;
  const end = start + 10;
  visiblePosts.value = [...visiblePosts.value, ...posts.value.slice(start, end)];
};

const handleScroll = () => {
  const endOfList = document.querySelector('.battlestations__end-of-list');
  const endOfListPosition = endOfList.getBoundingClientRect().top + window.scrollY;
  if (window.innerHeight + window.scrollY >= endOfListPosition) {
    loadMore();
  }
};

const isValidImage = (url) => {
  return url && !url.includes('imgur.com');
};

onMounted(async () => {
  try {
    const response = await fetch('https://www.reddit.com/r/battlestations/hot.json');
    const data = await response.json();
    const children = data.data.children;
    for (let i = 0; i < children.length; i++) {
      const post = children[i].data;
      if (isValidImage(post.url)) {
        posts.value.push(post);
      }
    }
    visiblePosts.value = posts.value.slice(0, 10);
    isLoading.value = false;

    window.addEventListener('scroll', handleScroll);
  } catch (error) {
    console.error(error);
    isLoading.value = false;
    error.value = error.message;
  }
});
</script>
