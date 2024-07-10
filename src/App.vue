<template>
  <div class="container">
    <h1>猫</h1>
    <div>
      <select v-model="selectedTag">
        <option value="">全てのタグ</option>
        <option v-for="tag in uniqueTags" :key="tag" :value="tag">{{ tag }}</option>
      </select>
    </div>
    <div>
      <input type="text" v-model="searchQuery" placeholder="検索する">
    </div>
    <div class="images-container">
      <div v-for="catImage in filteredCatImages" :key="catImage._id" class="image-card">
        <img :src="`${IMAGE_URL_PREFIX}${catImage._id}`" @click="openModal(catImage._id)" >
        <p>{{catImage.tags}}</p>
        <button @click="toggleFavorite(catImage._id)" :class="{ favorite: isFavorite(catImage._id) }">
          {{ isFavorite(catImage._id) ? 'お気に入りから削除' : 'お気に入りに追加' }}
        </button>
      </div>
    </div>
    <Modal :isVisible="isModalVisible" :imageSrc="selectedImageSrc" @close="closeModal"/>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import Modal from './components/Modal.vue';

const FAVORITE_TAG = 'お気に入り';
const CAT_API_URL = 'https://cataas.com/api/cats';
const IMAGE_URL_PREFIX = 'https://cataas.com/cat/';
const FAVORITES_KEY = 'favorites';

const catImages = ref([]);
const selectedTag = ref('');
const isModalVisible = ref(false);
const selectedImageSrc = ref('');
const favorites = ref(new Set<string>());
const searchQuery = ref('');

const uniqueTags = computed(() => {
  const tags = catImages.value.flatMap(catImage => catImage.tags);
  const uniqueTagsSet = new Set(tags);
  uniqueTagsSet.add(FAVORITE_TAG);
  return [...uniqueTagsSet];
});

const filteredCatImages = computed(() => {
  let filteredImages = catImages.value;

  if (selectedTag.value === FAVORITE_TAG) {
    filteredImages = filteredImages.filter(catImage => favorites.value.has(catImage._id));
  } else if (selectedTag.value) {
    filteredImages = filteredImages.filter(catImage => catImage.tags.includes(selectedTag.value));
  }

  if (searchQuery.value) {
    filteredImages = filteredImages.filter(catImage => 
      catImage.tags.some(tag => tag.includes(searchQuery.value))
    );
  }

  return filteredImages;
});

const fetchCatImages = async () => {
  try {
    let response = await fetch(CAT_API_URL);
    if (!response.ok) {
      throw new Error('Network error');
    }
    const data = await response.json();
    catImages.value = data;
  } catch (error) {
    console.error(error);
  }
}

const openModal = (imageId: string) => {
  selectedImageSrc.value = `${IMAGE_URL_PREFIX}${imageId}`;
  isModalVisible.value = true;
};

const closeModal = () => {
  isModalVisible.value = false;
  selectedImageSrc.value = '';
};

const toggleFavorite = (imageId: string) => {
  if (favorites.value.has(imageId)) {
    favorites.value.delete(imageId);
  } else {
    favorites.value.add(imageId);
  }
  saveFavoritesToLocalStorage();
};

const isFavorite = (imageId: string) => {
  return favorites.value.has(imageId);
};

const saveFavoritesToLocalStorage = () => {
  const favoriteArray = Array.from(favorites.value);
  localStorage.setItem(FAVORITES_KEY, JSON.stringify(favoriteArray));
};

const loadFavoritesFromLocalStorage = () => {
  const favoriteArray = JSON.parse(localStorage.getItem(FAVORITES_KEY) || '[]');
  favorites.value = new Set(favoriteArray);
};

onMounted(() => {
  fetchCatImages();
  loadFavoritesFromLocalStorage();
});
</script>

<style>
.container {
  position: absolute;
  top: 50px;
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

button {
  background-color: #ffeb3b;
  border: none;
  padding: 5px 10px;
  margin-top: 10px;
  cursor: pointer;
}

button:hover {
  background-color: #fdd835;
}

button.favorite {
  background-color: #ff6666;
}

button.favorite:hover {
  background-color: #ff3333;
}

input[type="text"] {
  padding: 10px;
  margin: 10px 0;
  width: 20%;
  height: 20px;
  box-sizing: border-box;
}
</style>
