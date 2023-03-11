<script setup>
import { onMounted, ref } from 'vue'
import useAPI from '@/composables/useAPI'
import BaseTitle from '@/components/BaseTitle.vue'

const { categories, getCategories } = useAPI()

onMounted(async() => {
  await getCategories()
})
</script>

<template>
  <BaseTitle>
    <!-- <img class="logo" src="logo.svg" alt="logo" /> -->
    <template #logo>
      <img src="logo.svg" alt="logo" />
    </template>
    Triviatastic
    <!-- <img class="logo" src="logo.svg" alt="logo" /> -->
  </BaseTitle>
  <div v-if="categories.length > 0" class="categories">
    <RouterLink :to="`/question/category/${category.id}`" 
    v-for="category in categories" :key="category.id" 
    class="category">{{ category.name }}</RouterLink>
  </div>
</template>

<style lang="postcss" scoped>

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