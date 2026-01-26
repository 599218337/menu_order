<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
  dishes: {
    type: Array,
    required: true
  },
  cart: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['select', 'addToCart'])

const isRolling = ref(false)
const currentDish = ref(null)
const animationInterval = ref(null)
const selectedCategory = ref('全部')
const consecutiveSkips = ref(0)
const showEatOutPrompt = ref(false)

const categories = ['全部', '主食', '小炒', '大菜', '汤', '甜品', '出去吃']

// Filter dishes based on category
const filteredDishes = computed(() => {
  let list = props.dishes

  // Filter by category
  if (selectedCategory.value === '全部') {
    list = list.filter(d => d.category !== '出去吃')
  } else {
    list = list.filter(d => d.category === selectedCategory.value)
  }

  // Filter out already selected items
  return list.filter(d => !props.cart.find(c => c.id === d.id))
})

const startRolling = () => {
  if (isRolling.value) return

  // Reset eat out prompt if it was shown
  showEatOutPrompt.value = false

  isRolling.value = true
  let counter = 0
  const maxCount = 20
  const speed = 100

  // Use filtered list, or fallback to all if empty (shouldn't happen with good data)
  const pool = filteredDishes.value.length > 0 ? filteredDishes.value : props.dishes

  animationInterval.value = setInterval(() => {
    const randomIndex = Math.floor(Math.random() * pool.length)
    currentDish.value = pool[randomIndex]
    counter++

    if (counter > maxCount) {
      clearInterval(animationInterval.value)
      isRolling.value = false
      celebrate()
      handleRollResult()
    }
  }, speed)
}

const handleRollResult = () => {
  consecutiveSkips.value++
  if (consecutiveSkips.value >= 5) {
    showEatOutPrompt.value = true
  }
}

const selectCurrentDish = () => {
  if (currentDish.value) {
    emit('addToCart', currentDish.value)
    consecutiveSkips.value = 0 // Reset skips on success
    currentDish.value = null // Reset view to allow next pick
  }
}

const celebrate = () => {
  const app = document.getElementById('app')
  if (app) {
    app.classList.add('celebrate')
    setTimeout(() => {
      app.classList.remove('celebrate')
    }, 2000)
  }
}

const triggerEatOut = () => {
  // Find an eat out option
  const eatOutOptions = props.dishes.filter(d => d.category === '出去吃')
  if (eatOutOptions.length > 0) {
    currentDish.value = eatOutOptions[Math.floor(Math.random() * eatOutOptions.length)]
    showEatOutPrompt.value = false
    consecutiveSkips.value = 0
  }
}
</script>

<template>
  <div class="random-selector">
    <!-- Category Filter -->
    <div class="categories">
      <button v-for="cat in categories" :key="cat" :class="{ active: selectedCategory === cat }"
        @click="selectedCategory = cat">
        {{ cat }}
      </button>
    </div>

    <!-- Main Card -->
    <div class="card" :class="{ 'shake': isRolling, 'clickable': currentDish && !isRolling }"
      @click="currentDish && !isRolling ? emit('select', currentDish) : null">
      <div v-if="currentDish" class="emoji">{{ currentDish.emoji }}</div>
      <div v-else class="emoji">❓</div>

      <h2 v-if="currentDish">{{ currentDish.name }}</h2>
      <h2 v-else>今天吃点啥？</h2>

      <div v-if="currentDish" class="dish-meta">
        <span class="tag">{{ currentDish.category }}</span>
      </div>
    </div>

    <!-- Action Area -->
    <div class="actions">
      <button @click="startRolling" :disabled="isRolling" class="action-btn">
        {{ isRolling ? '纠结中...' : (currentDish ? '换一个' : '帮我选！') }}
      </button>

      <button v-if="currentDish && !isRolling" class="confirm-btn" @click.stop="selectCurrentDish">
        就它了！❤️
      </button>
    </div>

    <!-- Indecision Prompt -->
    <div v-if="showEatOutPrompt" class="prompt-overlay">
      <p>不行咱们出去吃吧？🤔</p>
      <button @click="triggerEatOut">走！出去吃！</button>
    </div>
  </div>
</template>

<style scoped>
.random-selector {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  padding-bottom: 100px;
  /* Space for cart */
}

.categories {
  display: flex;
  gap: 0.5rem;
  overflow-x: auto;
  width: 100%;
  padding: 0.5rem 1rem;
  margin-bottom: 1rem;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  /* Firefox */
}

.categories::-webkit-scrollbar {
  display: none;
  /* Chrome/Safari */
}

.categories button {
  white-space: nowrap;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  border: 1px solid #FFAB91;
  background: white;
  color: #FF8A65;
  font-family: inherit;
  font-size: 0.9rem;
}

.categories button.active {
  background: #FF8A65;
  color: white;
}

.card {
  background: white;
  padding: 2rem;
  border-radius: 24px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  width: 85%;
  max-width: 400px;
  min-height: 320px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  transition: transform 0.3s;
  margin-bottom: 2rem;
  position: relative;
}

.emoji {
  font-size: 6rem;
  margin-bottom: 1rem;
}

h2 {
  margin: 0;
  color: #5D4037;
  font-size: 2rem;
  text-align: center;
}

.dish-meta {
  margin-top: 1rem;
}

.tag {
  background: #FFF3E0;
  color: #FF8A65;
  padding: 0.2rem 0.8rem;
  border-radius: 10px;
  font-size: 0.9rem;
}

.actions {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 100%;
  align-items: center;
}

.action-btn {
  background-color: #FF8A65;
  color: white;
  border: none;
  padding: 1.2rem 3rem;
  font-size: 1.5rem;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(255, 138, 101, 0.3);
  font-family: inherit;
  width: 80%;
  max-width: 300px;
}

.confirm-btn {
  background-color: #81C784;
  color: white;
  border: none;
  padding: 1rem 3rem;
  font-size: 1.3rem;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(129, 199, 132, 0.3);
  font-family: inherit;
  width: 80%;
  max-width: 300px;
  animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.prompt-overlay {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: white;
  padding: 2rem;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.2);
  text-align: center;
  z-index: 200;
  width: 80%;
}

.prompt-overlay button {
  background: #FF8A65;
  color: white;
  border: none;
  padding: 0.8rem 2rem;
  border-radius: 20px;
  font-family: inherit;
  margin-top: 1rem;
  font-size: 1.2rem;
}

@keyframes popIn {
  from {
    transform: scale(0.8);
    opacity: 0;
  }

  to {
    transform: scale(1);
    opacity: 1;
  }
}

.shake {
  animation: shake 0.5s infinite;
}
</style>
