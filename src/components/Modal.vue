<template>
  <div v-if="props.isVisible" class="modal">
    <div class="modal-content">
      <span class="close" @click="closeModal">&times;</span>
      <img :src="props.imageSrc" class="modal-img">
    </div>
  </div>
</template>

<script setup lang="ts">
  const props = defineProps({
    isVisible: Boolean,
    imageSrc: String
  });

  const emit = defineEmits(['close']);

  const closeModal = () => {
    emit('close');
  };
</script>


<style>
.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0,0,0,0.4);
}

.modal-content {
  background-color: #fefefe;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: auto;
  height: 50%;
  overflow: auto;
  border-radius: 8px;
  text-align: center;
  position: relative;
}

.close {
    position: absolute;
    right: 10px;
    top: 10px;
    color: #aaa;
    font-size: 28px;
    font-weight: bold;
    cursor: pointer;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

.modal-img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  border-radius: 8px;
}
</style>