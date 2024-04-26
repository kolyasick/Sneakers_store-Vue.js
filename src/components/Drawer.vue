<script setup>
import DrawerHead from './DrawerHead.vue';
import CartItemList from './CartItemList.vue';
import infoBlock from './infoBlock.vue';
import axios from "axios";
import {computed, inject, ref} from "vue";

const props = defineProps({
    totalPrice: Number,
    vatPrice: Number,
})


const {cartItems, closeDrawer} = inject('cartItems')

const isCreating = ref(false);
const orderId = ref(null);


const createOrder = async () => {
  try {
    isCreating.value = true

    const { data } = await axios.post('https://2e99c84f6f8e8adc.mokky.dev/orders', {
      items: cartItems.value,
      totalPrice: props.totalPrice.value,
    })

    cartItems.value = []

    orderId.value = data.id
  } catch(err) {
    console.log(err)
  } finally {
    isCreating.value = false
  }
}
const cartIsEmpty = computed(() => cartItems.value.length === 0)
const buttonDisabled = computed(() => isCreating.value || cartIsEmpty.value)
</script>

<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
       <DrawerHead />

        <div v-if="!totalPrice || orderId" class="flex h-full items-center">
          <infoBlock
              v-if="!totalPrice && !orderId"
              title="Корзина пустая"
              description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
              image-url="/package-icon.png"
          />
          <infoBlock
              v-if="orderId"
              title="Заказ оформлен"
              :description="`Ваш заказ #${orderId} скоро будет передан курьерской службе.`"
              image-url="/order-success-icon.png"
          />
        </div>


       <div v-else>
         <CartItemList />

         <div class="flex flex-col gap-4 mt-7">
           <div class="flex gap-2">
             <span>Итого:</span>
             <div class="flex-1 border-b border-dashed"></div>
             <b>{{totalPrice}} руб.</b>
           </div>

           <div class="flex gap-2">
             <span>Налог 5%:</span>
             <div class="flex-1 border-b border-dashed"></div>
             <b>{{ vatPrice }} руб.</b>
           </div>

           <button
             :disabled="buttonDisabled"
             class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white cursor-pointer disabled:bg-slate-300 hover:bg-lime-600 transition active:bg-lime-700"
             @click="createOrder"
           >
             Оформить заказ
           </button>
         </div>
       </div>
    </div>
</template>