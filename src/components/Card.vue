<script setup>

defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function
})

</script>

<template>
    <div class="relative max-w-full bg-white border border-slate-200 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl max-lg:flex max-lg:flex-col max-lg:items-center">

        <img v-if="onClickFavorite" @click="onClickFavorite" :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'" alt="Like 1" class="absolute top-8 left-8">

        <img class="max-h-60 " :src="imageUrl" alt="Sneaker">

        <p class="mt-2">{{ title }}</p>

        <div class="flex justify-between w-full mt-5">
          <div class="flex flex-col">
            <span class="text-slate-400">Цена:</span>
            <b>{{ price }} руб.</b>
          </div>

          <img v-if="onClickFavorite" @click="onClickAdd" :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Plus">
        </div>
      </div>
</template>