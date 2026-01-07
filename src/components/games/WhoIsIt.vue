<template>
  <div class="who-is-it-game">
    <!-- Game Header -->
    <div class="game-header">
      <div class="score-board">
        <span class="score-label">Score:</span>
        <span class="score-value">{{ score }}</span>
      </div>
      <div class="progress-info">
        <span>Question {{ currentQuestion }} / {{ totalQuestions }}</span>
      </div>
      <Button
        icon="pi pi-times"
        class="close-button"
        rounded
        @click="$emit('close')"
        aria-label="Close game"
      />
    </div>

    <!-- Question Display -->
    <div v-if="question" class="question-section">
      <!-- Word Question -->
      <div v-if="question.type === 'word'" class="question-display word-question">
        <h2 class="question-title">Find the picture for:</h2>
        <div class="question-content">
          <span class="question-word">{{ question.content }}</span>
        </div>
      </div>

      <!-- Picture Question -->
      <div v-else-if="question.type === 'picture'" class="question-display picture-question">
        <h2 class="question-title">What is this?</h2>
        <div class="question-image-container">
          <img :src="question.image" :alt="question.content" class="question-image" />
        </div>
      </div>

      <!-- Sign Language Video Question -->
      <div v-else-if="question.type === 'sign'" class="question-display sign-question">
        <h2 class="question-title">What sign is this?</h2>
        <VideoPlayer
          :video-src="question.videoSrc"
          :gif-src="question.gifSrc"
          :use-video="true"
        />
      </div>
    </div>

    <!-- Answer Options -->
    <div class="options-grid">
      <div
        v-for="option in options"
        :key="option.id"
        class="option-card"
        :class="{
          selected: selectedOption === option.id,
          correct: showResult && option.id === correctAnswer,
          incorrect: showResult && selectedOption === option.id && option.id !== correctAnswer
        }"
        @click="selectOption(option.id)"
      >
        <!-- Picture Option -->
        <div v-if="option.type === 'picture'" class="option-content">
          <div class="option-image-wrapper">
            <img :src="option.image" :alt="option.caption" class="option-image" />
          </div>
          <p class="option-caption">{{ option.caption }}</p>
        </div>

        <!-- Word Option -->
        <div v-else-if="option.type === 'word'" class="option-content">
          <span class="option-word">{{ option.content }}</span>
        </div>

        <!-- Result Icon -->
        <div v-if="showResult" class="result-icon">
          <i
            v-if="option.id === correctAnswer"
            class="pi pi-check-circle"
          ></i>
          <i
            v-else-if="selectedOption === option.id"
            class="pi pi-times-circle"
          ></i>
        </div>
      </div>
    </div>

    <!-- Feedback Message -->
    <transition name="fade">
      <div v-if="showResult" class="feedback-message" :class="{ correct: isCorrect, incorrect: !isCorrect }">
        <i :class="isCorrect ? 'pi pi-check' : 'pi pi-times'"></i>
        <span>{{ isCorrect ? 'Great job!' : 'Try again!' }}</span>
      </div>
    </transition>

    <!-- Next Button -->
    <div v-if="showResult" class="next-button-container">
      <Button
        :label="currentQuestion === totalQuestions ? 'Finish' : 'Next Question'"
        icon="pi pi-arrow-right"
        iconPos="right"
        class="next-button"
        size="large"
        @click="nextQuestion"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import Button from 'primevue/button'
import VideoPlayer from '@/components/common/VideoPlayer.vue'

const props = defineProps({
  themeId: {
    type: String,
    required: true
  },
  variation: {
    type: String,
    default: 'word-to-picture'
  }
})

const emit = defineEmits(['close', 'finish'])

// Game state
const score = ref(0)
const currentQuestion = ref(1)
const totalQuestions = ref(10)
const selectedOption = ref(null)
const showResult = ref(false)
const isCorrect = ref(false)

// Mock question data (in real app, generate from theme data)
const question = ref({
  type: 'word',
  content: 'Dog'
})

const correctAnswer = ref('dog')

const options = ref([
  {
    id: 'dog',
    type: 'picture',
    image: '/assets/images/themes/animals/dog.jpg',
    caption: 'Dog'
  },
  {
    id: 'cat',
    type: 'picture',
    image: '/assets/images/themes/animals/cat.jpg',
    caption: 'Cat'
  },
  {
    id: 'elephant',
    type: 'picture',
    image: '/assets/images/themes/animals/elephant.jpg',
    caption: 'Elephant'
  }
])

const selectOption = (optionId) => {
  if (showResult.value) return

  selectedOption.value = optionId
  isCorrect.value = optionId === correctAnswer.value
  showResult.value = true

  if (isCorrect.value) {
    score.value += 10
  }
}

const nextQuestion = () => {
  if (currentQuestion.value === totalQuestions.value) {
    emit('finish', { score: score.value, total: totalQuestions.value })
  } else {
    currentQuestion.value++
    selectedOption.value = null
    showResult.value = false
    // Load next question (mock - in real app, generate from data)
  }
}
</script>

<style scoped>
.who-is-it-game {
  padding: 24px;
  max-width: 1200px;
  margin: 0 auto;
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 32px;
  padding: 20px;
  background: white;
  border-radius: 16px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.score-board {
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 1.6rem;
  font-weight: bold;
}

.score-label {
  color: #666;
}

.score-value {
  color: #f5576c;
  font-size: 2rem;
}

.progress-info {
  font-size: 1.4rem;
  color: #666;
  font-weight: 600;
}

.close-button {
  width: 50px;
  height: 50px;
  font-size: 1.5rem;
  background: #f44336;
}

.question-section {
  margin-bottom: 40px;
}

.question-display {
  text-align: center;
  padding: 32px;
  background: white;
  border-radius: 20px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
}

.question-title {
  font-size: 2rem;
  color: #333;
  margin: 0 0 24px 0;
  font-weight: bold;
}

.question-word {
  font-size: 4rem;
  font-weight: bold;
  color: #667eea;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.question-image-container {
  max-width: 400px;
  margin: 0 auto;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
}

.question-image {
  width: 100%;
  height: auto;
  display: block;
}

.options-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 24px;
  margin-bottom: 32px;
}

.option-card {
  background: white;
  border-radius: 20px;
  padding: 24px;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 4px solid transparent;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  position: relative;
}

.option-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
  border-color: #667eea;
}

.option-card.selected {
  border-color: #667eea;
  transform: scale(1.05);
}

.option-card.correct {
  border-color: #4caf50;
  background: #e8f5e9;
  animation: correctPulse 0.6s ease;
}

.option-card.incorrect {
  border-color: #f44336;
  background: #ffebee;
  animation: shake 0.5s ease;
}

.option-content {
  text-align: center;
}

.option-image-wrapper {
  width: 100%;
  aspect-ratio: 1;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 16px;
  background: #f5f5f5;
}

.option-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.option-caption {
  font-size: 1.4rem;
  font-weight: bold;
  color: #333;
  margin: 0;
}

.option-word {
  font-size: 2.5rem;
  font-weight: bold;
  color: #333;
  display: block;
  padding: 40px 20px;
}

.result-icon {
  position: absolute;
  top: 12px;
  right: 12px;
  font-size: 2.5rem;
}

.result-icon .pi-check-circle {
  color: #4caf50;
}

.result-icon .pi-times-circle {
  color: #f44336;
}

.feedback-message {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  padding: 24px;
  border-radius: 16px;
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 24px;
  animation: slideIn 0.4s ease-out;
}

.feedback-message.correct {
  background: #4caf50;
  color: white;
}

.feedback-message.incorrect {
  background: #ff9800;
  color: white;
}

.feedback-message i {
  font-size: 2.5rem;
}

.next-button-container {
  text-align: center;
}

.next-button {
  font-size: 1.6rem;
  padding: 20px 48px;
  border-radius: 16px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
}

.next-button:hover {
  transform: scale(1.05);
}

/* Animations */
@keyframes correctPulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-10px); }
  75% { transform: translateX(10px); }
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

@media (max-width: 768px) {
  .who-is-it-game {
    padding: 16px;
  }

  .game-header {
    flex-wrap: wrap;
    gap: 12px;
  }

  .question-word {
    font-size: 2.5rem;
  }

  .options-grid {
    grid-template-columns: 1fr;
  }
}
</style>
