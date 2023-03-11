<script setup>
import { onMounted, ref } from 'vue'
import { useRoute } from 'vue-router'

import useAPI from '@/composables/useAPI'
import useColors from '@/composables/useColor'
import BaseTitle from '@/components/BaseTitle.vue'

const route = useRoute()
const colors = useColors()
const api = useAPI()
const question = ref(null)
const answers = ref([])

onMounted(async () => {
  question.value = await api.getQuestion(route.params.id)
  answers.value.push({
    id:answers.value.length,
    correct: true,
    answer: question.value.correct_answer
  })
  question.value.incorrect_answers.map((answer) => {
    answers.value.push({
      id:answers.value.length,
      correct: false,
      answer,
    })
  })
})

</script>

<template>
  <div v-if="question" class="question-container">
    <BaseTitle>{{ question.category }}</BaseTitle>
    <p class="question">{{ question.question }}</p>
    <div class="answers">
      <div :class="colors.getColor(answer.id)" v-for="answer in answers" :key="answer.id" class="answer">
        {{ answer.answer }}
      </div>
    </div>
  </div>
  <div v-else class="loading">Loading...</div>
  
</template>

<style lang="postcss" scoped>
.question-container {
  @apply h-full w-full flex flex-col items-center gap-8;

  & .question {
    @apply text-2xl font-bold text-center;
  }

  & .answers {
    @apply w-full flex-grow grid grid-cols-2 gap-8;
    & .answer {
      @apply text-4xl text-center flex items-center justify-center rounded-lg
      text-white;
    }
  }
}

.loading {
  @apply flex justify-center items-center h-full w-full text-6xl;
}
</style>
