<script setup>
import { ref, watch, provide, computed } from 'vue';
import axios from 'axios';

import Header from './components/Header.vue';
import Drawer from './components/Drawer.vue';



const sortBy = ref('');
const searchQuery = ref('');

/* Cart (BEGIN) */

const cartItems = ref([]);


const drawerOpen = ref(false);

const totalPrice = computed(() => cartItems.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round(totalPrice.value * 0.05))




const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
    cartItems.value.push(item)
    item.isAdded = true
}

const removeFromCart = (item) => {
    cartItems.value.splice(cartItems.value.indexOf(item), 1)
    item.isAdded = false
}



watch(cartItems, () => {
      localStorage.setItem('cart', JSON.stringify(cartItems.value))
    },
    {deep: true}
)

provide('cartItems', {
  cartItems,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart,
});

/* Cart (END)*/

</script>

<template>
  <Drawer
      v-if="drawerOpen"
      :total-price="totalPrice"
      :vat-price="vatPrice"
  />
  
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>

<style scoped></style>
