<script setup>
import { ref, watch, nextTick } from 'vue'

const searchTerm = ref('')
const products = ref([])
let loading = ref(false)

const _debounce = (fn, timeout) => {
  let timer = null
  return arg => {
    clearTimeout(timer)
    timer = setTimeout(() => {
      fn(arg)
    }, timeout)
  }
}

const findProducts = async term => {
  if (!term || !term.trim()) {
    products.value = []
    return
  }
  try {
    loading.value = true
    const response = await (await fetch(`https://dummyjson.com/products/search?q=${term.trim()}`)).json()
    products.value = response.products
    loading.value = false
  } catch (e) {
    loading.value = false
    alert(e)
  }
}

const debouncedFn = _debounce(findProducts, 300)

watch(searchTerm, newTerm => debouncedFn(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div class="lds-hourglass" v-if="loading"></div>
    <ul class="list-disc" v-if="!!products.length && !loading">
      <li v-for="product in products" v-bind:key="product.id">{{ product.title }} - {{ product.price }}</li>
    </ul>
  </div>
</template>
<style>
.lds-hourglass {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.lds-hourglass:after {
  content: ' ';
  display: block;
  border-radius: 50%;
  width: 0;
  height: 0;
  margin: 8px;
  box-sizing: border-box;
  border: 32px solid #dfc;
  border-color: #dfc transparent #dfc transparent;
  animation: lds-hourglass 1.2s infinite;
}
@keyframes lds-hourglass {
  0% {
    transform: rotate(0);
    animation-timing-function: cubic-bezier(0.55, 0.055, 0.675, 0.19);
  }
  50% {
    transform: rotate(900deg);
    animation-timing-function: cubic-bezier(0.215, 0.61, 0.355, 1);
  }
  100% {
    transform: rotate(1800deg);
  }
}
</style>
