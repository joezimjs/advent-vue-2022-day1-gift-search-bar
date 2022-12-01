<script setup>
import { ref, watch } from 'vue'
import { useAsyncState } from '@vueuse/core'

const {
  state: products,
  isLoading,
  execute,
} = useAsyncState(findProducts, [], {
  immediate: false,
  delay: 300,
  resetOnExecute: false,
})

const searchTerm = ref('')
// const products = ref([])

async function findProducts(term) {
  if (term == '') {
    return (products.value = [])
  }
  const res = await fetch('https://dummyjson.com/products/search?q=phone')
  const data = await res.json()
  console.log(data)
  return data.products
}

watch(searchTerm, newTerm => execute(300, newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div v-if="isLoading">Loading...</div>
    <ul class="list-disc" v-if="products && products.length > 0 && !isLoading">
      <li v-for="product in products">{{ product.title }} - ${{ product.price }}</li>
    </ul>
  </div>
</template>
