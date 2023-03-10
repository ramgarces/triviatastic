<script setup>
import { onMounted, ref } from 'vue'
import useAPI from '@/composables/useAPI'

const api = useAPI()
const categories = ref([])

onMounted(async() => {
  categories.value = await api.getCategories()
})
</script>

<template>
  <div class="brand">
    <img class="logo" src="logo.svg" alt="logo" />
    <h1 class="title">Triviatastic</h1>
    <img class="logo" src="logo.svg" alt="logo" />
  </div>
  <div class="categories">
    <RouterLink :to="`/question/category/${category.id}`" 
    v-for="category in categories" :key="category.id" 
    class="category">{{ category.name }}</RouterLink>
  </div>
</template>

<style lang="postcss" scoped>
.brand {
  @apply flex items-center justify-center gap-4;

  & .logo {
    @apply h-16 w-16;
  }

  & .title {
    @apply text-6xl font-thin tracking-widest text-slate-700 uppercase;
  }
}

.categories {
  @apply grid flex-grow grid-cols-4 gap-12;

  & .category {
    @apply flex h-32 items-center justify-center rounded-lg 
    border-purple-700 py-4 border-4 font-bold uppercase text-slate-600
    transition-colors duration-300 text-center;
    
    &:hover {
      @apply cursor-pointer border-yellow-500 bg-yellow-500 text-slate-700;
    }
  }
}

</style>