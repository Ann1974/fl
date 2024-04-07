<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

import CardList from '../components/CardList.vue'

const favorites = ref([])
onMounted(async () => {
  try {
    const { data } = await axios.get(
      `https://209e4e169cdaed8c.mokky.dev/favorites?_relations=items`
    )
    favorites.value = data.map((obj) => obj.item)
  } catch (err) {
    console.log(err)
  }
})
</script>
<template>
  <h1>Моё избранное</h1>
  <h2 class="text-3xl font-bold mb-8">Моё избранное</h2>
  <CardList :items="favorites" is-favorites />
</template>
