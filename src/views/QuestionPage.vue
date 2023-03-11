<script setup>
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { shuffle } from 'lodash-es'

import NotificationAnswer from '@/components/NotificationAnswer.vue'

import useAPI from '@/composables/useAPI'
import useColors from '@/composables/useColor'
import BaseTitle from '@/components/BaseTitle.vue'
import useScore from '@/composables/useScore'
import DifficultyChip from '@/components/DifficultyChip.vue'

const route = useRoute()
const router = useRouter()
const colors = useColors()
const api = useAPI()
const question = ref(null)
const answers = ref([])
const showNotification = ref(false)
const isCorrect = ref(false)

const { changeScore } = useScore()
const handleAnswer = (points) => {
  isCorrect.value = points > 0
  showNotification.value = true
  //wait 3 seconds
  setTimeout(() => {
    changeScore(points)
    router.push('/')
  }, 700)
  
}

onMounted(async () => {
  question.value = await api.getQuestion(route.params.id)
  answers.value.push({
    id:answers.value.length,
    correct: true,
    answer: question.value.correct_answer,
    points: question.value.difficulty === 'easy' ? 10 :
            question.value.difficulty === 'medium' ? 20 : 30,
  })
  question.value.incorrect_answers.map((answer) => {
    answers.value.push({
      id:answers.value.length,
      correct: false,
      answer,
      points: -5,
    })
  })
  answers.value = shuffle(answers.value)
})

</script>

<template>
  <div v-if="question" class="question-container">
    <BaseTitle>{{ question.category }}</BaseTitle>
    <p class="question">{{ question.question }}</p>
    <div class="answers">
      <div :class="colors.getColor(answer.id)" v-for="answer in answers" 
      :key="answer.id" class="answer" @click="handleAnswer(answer.points)">
        {{ answer.answer }}
      </div>
    </div>
    <DifficultyChip :difficulty="question.difficulty"/>
  </div>
  <div v-else class="loading">Loading...</div>
  <NotificationAnswer :correct="isCorrect" v-if="showNotification"/>
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
      &:hover {
        @apply cursor-pointer;
      }
    }
  }
}

.loading {
  @apply flex justify-center items-center h-full w-full text-6xl;
}
</style>
