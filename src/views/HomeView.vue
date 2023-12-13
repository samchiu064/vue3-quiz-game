<script setup>
import axios from 'axios'
import QuizForm from '@/components/QuizForm.vue'
import { onMounted, ref } from 'vue'

const questions = ref([])
const correctAnswers = ref(0)
const incorrectAnswers = ref(0)

const updateResult = (isCorrect) => {
  isCorrect ? correctAnswers.value++ : incorrectAnswers.value++
}

const getQuestions = async () => {
  const { data } = await axios.get('https://opentdb.com/api.php?amount=10&type=multiple')
  questions.value = data.results
}

onMounted(async () => {
  await getQuestions()
})
</script>

<template>
  <main class="flex justify-center">
    <div class="container flex flex-col items-center">
      <div class="border-b-4 p-4 w-1/2 text-center">
        Correct <span class="border p-2 bg-blue-500 text-white">{{ correctAnswers }}</span> ：
        <span class="border p-2 bg-red-500 text-white">{{ incorrectAnswers }}</span> Incorrect
      </div>
      <QuizForm :questions="questions" @update:result="updateResult" />
    </div>
  </main>
</template>
