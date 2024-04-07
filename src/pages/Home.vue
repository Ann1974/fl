<script setup>
import { ref } from 'vue'

const isDisplayingProducts = ref(true) // Flag to control content display

const storeInfoText = `
Прогулки по книжным полкам – важный атрибут жизни каждого книжного почитателя.
` // Store information text

const toggleContent = () => {
  isDisplayingProducts.value = !isDisplayingProducts.value
}
</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-normal mb-8 relative">
      <span
        class="absolute top-0 left-0 w-full h-full bg-gradient-to-r from-pink-400 via-purple-500 to-indigo-500 bg-clip-text"
        style="font-size: 40px; font-weight: 600; white-space: nowrap"
        >У нас есть что читать, чем творить, что дарить и где выбирать!
      </span>
    </h2>

    <div class="flex gap-4"></div>
  </div>

  <div class="mt-10">
    <div v-if="!isDisplayingProducts.value">
      {{ storeInfoText }}
    </div>
    <div class="page-content">
      <h1>О нас:</h1>
      <p>
        Мы предлагаем большой выбор книг, поддерживая длительные и дружелюбные партнёрские отношения
        с большинством издательств, работающих на российском книжном рынке. Мы любим книги и ценим
        любовь к книгам наших покупателей, поэтому стараемся сделать всё возможное, чтобы каждая
        книга нашла своего читателя.
      </p>
      <p>Опишите свою миссию, ценности, историю и команду.</p>
      <p></p>
    </div>

    <div class="flex gap-4"></div>
  </div>

  <div class="mt-10">
    <div v-if="!isDisplayingProducts.value">
      {{ storeInfoText }}
      <Image src="vue-sneakers/public/24.png" alt="Image of a bookstore" title="Книжный магазин" />
    </div>
    <CardList v-if="isDisplayingProducts.value" :items="[]" @add-to-cart="() => {}" />
  </div>
</template>
