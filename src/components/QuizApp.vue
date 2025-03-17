<template>
    <div class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4 w-full max-w-lg">
      <div v-if="showScore">
        <div class="text-center">
          <h2 class="text-2xl font-bold mb-4">Score: {{ score }}/{{ questions.length }}</h2>
          <button @click="restartQuiz" class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
            Restart
          </button>
        </div>
      </div>
      <div v-else>
        <div class="flex justify-between mb-3 font-bold">
            <p v-if="totalAnswered !== 0" class="text-green-600">Score: {{ score }}/{{ totalAnswered }}</p>
            <p class="text-blue-600">Question: {{ currentQuestion }}/{{ questions.length }}</p>
        </div>
        <div class="mb-4 flex items-center justify-between">
          <h2 class="text-xl font-semibold">{{ currentQuestionData.question }}</h2>
        </div>
        <div class="grid grid-cols-1 gap-4">
          <button
            v-for="(answer, index) in currentQuestionData.answers"
            :key="index"
            @click="selectAnswer(answer)"
            class="flex items-center justify-between px-4 py-2 border rounded hover:bg-gray-100"
          >
            <span>{{ answer }}</span>

            <CheckIcon v-if="selectedAnswer === currentQuestionData.correct && selectedAnswer === answer" class="w-6 h-6 text-center text-green-500" />
            <XMarkIcon v-else-if="selectedAnswer === answer" class="w-6 h-6 text-center text-red-600" />
          </button>
        </div>
      </div>
    </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import { CheckIcon, XMarkIcon } from "@heroicons/vue/24/solid";
import questionsData from '../questions.json'
  
interface Question {
    question: string;
    answers: string[];
    correct: string;
};
  
const questions = ref<Question[]>(questionsData);

const currentQuestion = ref(0);
const score = ref(0);
const totalAnswered = ref(0);
const showScore = ref(false);
const selectedAnswer = ref<string | null>(null);
  
const currentQuestionData = computed(() => {
    return questions.value[currentQuestion.value];
});
  
const selectAnswer = (answer: string) => {
    if (selectedAnswer.value !== null) return;
    
    totalAnswered.value++;
    selectedAnswer.value = answer;
    
    if (answer === currentQuestionData.value.correct) {
      score.value++;
    }
    
    setTimeout(() => {
      if (currentQuestion.value < questions.value.length - 1) {
        currentQuestion.value++;
        selectedAnswer.value = null;
      } else {
        showScore.value = true;
      }
    }, 700);
};

const restartQuiz = () => {
    currentQuestion.value = 0;
    score.value = 0;
    showScore.value = false;
    selectedAnswer.value = null;
};
</script>
  