<script setup>
import { ref, watch, provide, computed } from 'vue'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

const Cart = ref([])

const drawerOpen = ref(false)

const totalPrice = computed(() => Cart.value.reduce((acc, item) => acc + item.price, 0))

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
  const existingItem = Cart.value.find((cartItem) => cartItem.id === item.id)
  if (existingItem) {
    existingItem.quantity++ // Увеличить количество, если уже в корзине
  } else {
    Cart.value.push({ ...item, quantity: 1 }) // Добавить товар с quantity 1
  }
  item.isAdded = true // Пометить товар как добавленный для обновления UI
}
const removeFromCart = (item) => {
  const index = Cart.value.findIndex((cartItem) => cartItem.id === item.id)
  if (index > -1) {
    Cart.value.splice(index, 1)
  }
  item.isAdded = false
}

watch(
  Cart,
  () => {
    localStorage.setItem('Cart', JSON.stringify(Cart.value))
  },
  { deep: true }
)

provide('Cart', { Cart, closeDrawer, openDrawer, addToCart, removeFromCart })
</script>

<template>
  <Drawer v-if="drawerOpen" :total-price="totalPrice" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view> </router-view>
    </div>
  </div>
</template>
