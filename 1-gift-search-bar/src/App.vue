<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { vue3Debounce as debounce } from 'vue-debounce'

// Custom v-debounce directive declaration
const vDebounce = debounce({
  defaultTime: 300,
  fireOnEmpty: true,
  lock: true,
  trim: true,
})

const products = ref([])
const loading = ref(false)

const findProducts = async term => {
  if (!term) return products.value = []

  loading.value = true

  try {
    const { data } = await axios.get('https://dummyjson.com/products/search', { params: { q: term } })
    products.value = data.products
  } catch (error) {
    alert('Failed to fetch dummy data :(')
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>

    <input type="text" class="p-2 border-2 border-gray-dark" v-debounce="findProducts" placeholder="Start typing..." />
    <span v-if="loading" class="mt-4 animate-spin text-5xl" aria-busy="true" aria-label="Loading results">
      🤔
    </span>

    <ul v-else class="list-disc">
      <li v-if="products.length > 0" v-for="product in products">
        {{ product.title }}
        -
        <span class="text-green">${{ product.price }}</span>
      </li>

      <span v-else class="italic">Search for a product ya dingus</span>
    </ul>
  </div>
</template>
