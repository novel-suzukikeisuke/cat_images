<template>
  <div class="containar">
    <h1>猫</h1>
    <div>
      <select v-model="selectedTag">
        <option value="">全てのタグ</option>
        <option v-for="tag in uniqueTags" :key="tag" :value="tag">{{ tag }}</option>
      </select>
    </div>
    <div class="images-container">
      <div v-for="catImage in filteredCatImages" :key="catImage._id" class="image-card">
        <img :src="`https://cataas.com/cat/${catImage._id}`" @click="openModal(catImage._id)" >
        <p>{{catImage.tags}}</p>
      </div>
    </div>
    <Modal :isVisible="isModalVisible" :imageSrc="selectedImageSrc" @close="closeModal"/>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import Modal from './components/Modal.vue';

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

onMounted(() => {
  fetchCatImages();
});

const isModalVisible = ref(false);
const selectedImageSrc = ref('');

const openModal = (imageId: string) => {
  selectedImageSrc.value = `https://cataas.com/cat/${imageId}`;
  isModalVisible.value = true;
};

const closeModal = () => {
  isModalVisible.value = false;
  selectedImageSrc.value = '';
};
</script>

<style>
.containar {
  position: absolute;
  top: 20px;
  left: 20%;
  right: 20%;
}

.images-container {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.image-card {
  flex: 1 1 calc(25% - 10px);
  max-width: calc(25% - 10px);
}

img {
  width: 100%;
  height: 200px;
  object-fit: cover;
}
</style>
