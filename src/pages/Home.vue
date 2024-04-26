<script setup>
import CardList from "@/components/CardList.vue";
import axios from "axios";
import debounce from "lodash.debounce";
import {inject, onMounted, reactive, ref, watch} from "vue";

const {addToCart, removeFromCart, cartItems} = inject("cartItems")

const items = ref([]);

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
});

const onChangeSelect = event => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = debounce(event => {
  filters.searchQuery = event.target.value
}, 150)

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const addToFavorite = async (item) => {
  try {

    if (!item.isFavorite) {
      const obj = {
        item_id: item.id
      };

      item.isFavorite = true;

      const {data} = await axios.post(`https://2e99c84f6f8e8adc.mokky.dev/favorites`, obj);

      item.favoriteId = data.id;
    } else {
      item.isFavorite = false;
      await axios.delete(`https://2e99c84f6f8e8adc.mokky.dev/favorites/${item.favoriteId}`);
      item.favoriteId = null;
    }

  } catch (err) {
    console.log(err);
  }
}

const fetchFavorites = async () => {
  try{
    const {data: favorites } = await axios.get(`https://2e99c84f6f8e8adc.mokky.dev/favorites`);

    items.value = items.value.map(item => {
      const favorite = favorites.find(favorite => favorite.item_id === item.id);

      if (!favorite) {
        return item;
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    });
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try{
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const {data} = await axios.get(`https://2e99c84f6f8e8adc.mokky.dev/items`, {params});
    items.value = data.map(obj => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }));
  } catch (err) {
    console.log(err)
  }
}

watch(filters, fetchItems);

watch(cartItems, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

onMounted(async () => {
  const localCart = localStorage.getItem('cart');
  cartItems.value = localCart ? JSON.parse(localCart) : [];

  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cartItems.value.some((cartItem) => cartItem.id === item.id)
  }))
});
</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (дешевые)</option>
        <option value="-price">По цене (дорогие)</option>
      </select>

      <div class="relative">
        <img class="absolute left-4 top-3" src="/search.svg" alt="Search">
        <input @input="onChangeSearchInput" class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400 transition" placeholder="Поиск...">
      </div>
    </div>
  </div>

  <div class="mt-10">
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus"/>
  </div>
</template>

<style scoped>

</style>