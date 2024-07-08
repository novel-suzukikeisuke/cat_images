<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <div v-if="props.isVisible" class="modal">
        <div class="modal-content">
            <span class="close" @click="closeModal">&times;</span>
            <img :src="props.imageSrc" alt="Cat Image">
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
    display: block;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgb(0,0,0);
    background-color: rgba(0,0,0,0.4);
}

.modal-content {
    position: relative;
    margin: auto;
    padding: 0;
    width: 80%;
    max-width: 500px;
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

    img {
    width: 100%;
    height: auto;
    }
</style>
