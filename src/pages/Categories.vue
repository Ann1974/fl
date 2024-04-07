<script setup>
import { ref, onMounted, watch, reactive } from 'vue'
import axios from 'axios'
import CardList from '../components/CardList.vue'
import debounce from 'lodash.debounce'
import { inject } from 'vue'

const categories = ref([
  { id: 1, name: 'Фэнтези' },
  { id: 2, name: 'Фантастика' },
  { id: 3, name: 'Детектив' },
  { id: 4, name: 'Романтика' },
  { id: 5, name: 'Ужасы' },
  { id: 6, name: 'Наука' }
])
const selectedCategory = ref(null)
const items = ref([])

function selectCategory(categoryId) {
  selectedCategory.value = categoryId
}

onMounted(async () => {
  categories.value = await fetchCategories()
})

async function fetchCategories() {
  try {
    const response = await axios.get('https://209e4e169cdaed8c.mokky.dev/categories')
    if (response.status === 200) {
      return response.data
    } else {
      console.error('Ошибка при получении категорий:', response.statusText)
      return []
    }
  } catch (error) {
    console.error('Ошибка при получении категорий:', error)
    return []
  }
}

async function fetchItemsByCategory(categoryId) {
  try {
    const response = await axios.get(
      categoryId === 1
        ? 'https://209e4e169cdaed8c.mokky.dev/fantasy'
        : categoryId === 2
        ? 'https://209e4e169cdaed8c.mokky.dev/fantastic'
        : categoryId === 3
        ? 'https://209e4e169cdaed8c.mokky.dev/detective'
        : categoryId === 4
        ? 'https://209e4e169cdaed8c.mokky.dev/romantic'
        : categoryId === 5
        ? 'https://209e4e169cdaed8c.mokky.dev/horror'
        : categoryId === 6
        ? 'https://209e4e169cdaed8c.mokky.dev/science'
        : `https://209e4e169cdaed8c.mokky.dev/categories/${categoryId}/items`
    )
    if (response.status === 200) {
      return response.data
    } else {
      console.error('Ошибка при получении товаров по категории:', response.statusText)
      return []
    }
  } catch (error) {
    console.error('Ошибка при получении товаров по категории:', error)
    return []
  }
}

watch(selectedCategory, async (newValue) => {
  if (newValue) {
    items.value = await fetchItemsByCategory(newValue)
  }
})

const { Cart, addToCart, removeFromCart } = inject('Cart')

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id
      }

      item.isFavorite = true
      const { data } = await axios.post(`https://209e4e169cdaed8c.mokky.dev/favorites`, obj)
      item.favoriteId = data.id
    } else {
      item.isFavorite = false
      await axios.delete(`https://209e4e169cdaed8c.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(`https://209e4e169cdaed8c.mokky.dev/favorites`)
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.item_id === item.id)

      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get('https://209e4e169cdaed8c.mokky.dev/items', {
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('Cart')
  Cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: Cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(Cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)
</script>
<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-normal mb-8">Жанры книг:</h2>
  </div>
  <div class="categories-container">
    <ul class="list-none">
      <li v-for="category in categories" :key="category.id">
        <button @click="selectCategory(category.id)">{{ category.name }}</button>
      </li>
    </ul>

    <div v-if="selectedCategory">
      <h2 class="text-xl font-bold mb-4">
        {{ categories.find((cat) => cat.id === selectedCategory)?.name }}
      </h2>
      <CardList :items="items" @addToFavorite="addToFavorite" @add-to-cart="onClickAddPlus" />
    </div>
  </div>
</template>
