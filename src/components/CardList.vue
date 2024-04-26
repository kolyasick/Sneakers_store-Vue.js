<script setup>
import Card from './Card.vue'

defineProps({
  items: Array,
  isFavorites: Boolean
})

const emit = defineEmits(['addToFavorite', 'addToCart'])



</script>


<template>
     <div v-auto-animate class="grid  2xl:grid-cols-4 gap-5 xl:grid-cols-3 lg:grid-cols-2 md:grid-cols-1 sm:grid-cols-1">
        <Card 
        v-for="item in items"
        :key="item.id"
        :id="item.id"
        :title="item.title" 
        :imageUrl="item.imageUrl" 
        :price="item.price"
        :onClickFavorite="isFavorites ? null : () => emit('addToFavorite', item)"
        :onClickAdd="isFavorites ? null : () => emit('addToCart', item)"
        :isFavorite="item.isFavorite"
        :isAdded="item.isAdded"
        />
      </div>
</template>