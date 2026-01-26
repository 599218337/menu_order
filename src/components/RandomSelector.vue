<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  dishes: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['select'])

const isRolling = ref(false)
const currentDish = ref(null)
const animationInterval = ref(null)

const startRolling = () => {
  if (isRolling.value) return

  isRolling.value = true
  let counter = 0
  const maxCount = 20 // Number of flips before stopping
  const speed = 100 // Speed in ms

  animationInterval.value = setInterval(() => {
    const randomIndex = Math.floor(Math.random() * props.dishes.length)
    currentDish.value = props.dishes[randomIndex]
    counter++

    if (counter > maxCount) {
      clearInterval(animationInterval.value)
      isRolling.value = false
      celebrate()
    }
  }, speed)
}

const celebrate = () => {
  // Simple CSS celebration effect trigger
  const app = document.getElementById('app')
  app.classList.add('celebrate')
  setTimeout(() => {
    app.classList.remove('celebrate')
  }, 2000)
}
</script>

<template>
  <div class="random-selector">
    <div class="card" :class="{ 'shake': isRolling, 'clickable': currentDish && !isRolling }"
      @click="currentDish && !isRolling ? emit('select', currentDish) : null">
      <div v-if="currentDish" class="emoji">{{ currentDish.emoji }}</div>
      <div v-else class="emoji">❓</div>

      <h2 v-if="currentDish">{{ currentDish.name }}</h2>
      <h2 v-else>今天吃点啥？</h2>
    </div>

    <button @click="startRolling" :disabled="isRolling" class="action-btn">
      {{ isRolling ? '纠结中...' : '帮我选！' }}
    </button>
  </div>
</template>

<style scoped>
.random-selector {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 60vh;
  gap: 2rem;
}

.card {
  background: white;
  padding: 3rem;
  border-radius: 24px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  width: 80%;
  max-width: 300px;
  min-height: 250px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.clickable {
  cursor: pointer;
}

.clickable:hover {
  transform: scale(1.05);
}

.emoji {
  font-size: 5rem;
  margin-bottom: 1rem;
}

h2 {
  margin: 0;
  color: #5D4037;
  font-size: 1.5rem;
}

.shake {
  animation: shake 0.5s infinite;
}

@keyframes shake {
  0% {
    transform: translate(1px, 1px) rotate(0deg);
  }

  10% {
    transform: translate(-1px, -2px) rotate(-1deg);
  }

  20% {
    transform: translate(-3px, 0px) rotate(1deg);
  }

  30% {
    transform: translate(3px, 2px) rotate(0deg);
  }

  40% {
    transform: translate(1px, -1px) rotate(1deg);
  }

  50% {
    transform: translate(-1px, 2px) rotate(-1deg);
  }

  60% {
    transform: translate(-3px, 1px) rotate(0deg);
  }

  70% {
    transform: translate(3px, 1px) rotate(-1deg);
  }

  80% {
    transform: translate(-1px, -1px) rotate(1deg);
  }

  90% {
    transform: translate(1px, 2px) rotate(0deg);
  }

  100% {
    transform: translate(1px, -2px) rotate(-1deg);
  }
}

.action-btn {
  background-color: #FF8A65;
  color: white;
  border: none;
  padding: 1rem 2rem;
  font-size: 1.2rem;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(255, 138, 101, 0.3);
  transition: transform 0.2s, background-color 0.2s;
  font-family: inherit;
}

.action-btn:disabled {
  background-color: #FFCCBC;
  cursor: not-allowed;
  transform: none;
}

.action-btn:active:not(:disabled) {
  transform: scale(0.95);
  background-color: #F4511E;
}
</style>
