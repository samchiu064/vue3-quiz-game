<script setup>
import { ref, computed, toRefs } from 'vue'

const props = defineProps({
  questions: {
    type: Array,
    required: true
  }
})

const { questions } = toRefs(props)
const questionIndex = ref(0)
const selectedOption = ref('')
const isCorrect = ref(false)
const isSubmitted = ref(false)

const currentQuestion = computed(() => questions.value[questionIndex.value])
const correctAnswer = computed(() => currentQuestion.value?.correct_answer)
const incorrectAnswers = computed(() => currentQuestion.value?.incorrect_answers)
const options = computed(() =>
  incorrectAnswers.value?.concat(correctAnswer.value).sort(() => Math.random() - 0.5)
)

const emit = defineEmits(['update:result'])

const goNextQuiz = () => {
  questionIndex.value++
  isCorrect.value = false
  isSubmitted.value = false
}

const checkAnswer = () => {
  isCorrect.value = selectedOption.value === correctAnswer.value
  isSubmitted.value = true
  emit('update:result', isCorrect.value)
}
</script>

<template>
  <div class="flex flex-col gap-4">
    <p class="text-2xl font-bold mt-4" v-html="currentQuestion?.question"></p>
    <form class="grid gap-4 grid-cols-2">
      <label
        v-for="(option, key) in options"
        :key="option"
        :for="`option${key}`"
        class="p-2 border rounded-xl cursor-pointer hover:outline hover:outline-sky-600"
      >
        <input
          v-model="selectedOption"
          type="radio"
          name="option"
          :id="`option${key}`"
          :value="option"
        />
        <span class="px-2" v-html="option"></span>
      </label>
    </form>
    <p v-if="isSubmitted" class="mx-auto">
      {{
        isCorrect
          ? `✅ Congratulations, the answer ${correctAnswer} is correct.`
          : `❌ I'm sorry, you picked the wrong answer. The correct is ${correctAnswer}.`
      }}
    </p>
    <p v-if="questionIndex === questions.length && isSubmitted" class="mx-auto">
      You have completed the quiz. Thank you for participating.
    </p>
    <button
      v-if="questionIndex < questions.length"
      type="button"
      class="border border-bg-sky-600 hover:bg-sky-600 py-2 px-6 rounded w-64 mx-auto group"
      @click="isSubmitted ? goNextQuiz() : checkAnswer()"
    >
      <span class="text-black group-hover:text-white">{{
        isSubmitted ? 'Next Quiz' : 'Submit'
      }}</span>
    </button>
  </div>
</template>
