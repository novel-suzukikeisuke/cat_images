<template>
  <div>
    <h1>猫</h1>
    <div>
      <select v-model="selectedTag">
        <option value="">全てのタグ</option>
        <option v-for="tag in uniqueTags" :key="tag" :value="tag">{{ tag }}</option>
      </select>
    </div>
    <div class="images-container">
      <div v-for="catImage in filteredCatImages" :key="catImage._id" class="image-container">
        <img :src="`https://cataas.com/cat/${catImage._id}`" >
        <p>{{catImage.tags}}</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const catImages = ref([]);
const selectedTag = ref('');
const uniqueTags = computed(() => {
  const tags = catImages.value.flatMap(catImage => catImage.tags);
  return [...new Set(tags)];
});

const filteredCatImages = computed(() => {
  if (!selectedTag.value) {
    return catImages.value;
  }
  return catImages.value.filter(catImage => catImage.tags.includes(selectedTag.value));
});

const fetchCatImages = async () => {
  try {
    let response = await fetch('https://cataas.com/api/cats');
    if (!response.ok) {
      throw new Error('Network error');
    }
    const data = await response.json();
    catImages.value = data;
  } catch (error) {
    console.error(error);
  }
}

fetchCatImages();
</script>

<style>
.images-container {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.image-container {
  flex: 1 1 calc(25% - 10px);
  max-width: calc(25% - 10px);
}

img {
  width: 100%;
  height: auto;
}
</style>
