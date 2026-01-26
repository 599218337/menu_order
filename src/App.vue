<script setup>
import { ref, computed } from 'vue'
import dishesData from './assets/dishes.json'
import RandomSelector from './components/RandomSelector.vue'
import MenuList from './components/MenuList.vue'
import RecipeModal from './components/RecipeModal.vue'
import MenuCart from './components/MenuCart.vue'

const dishes = ref(dishesData)
const currentView = ref('random') // 'random', 'list', 'summary'
const selectedDish = ref(null)
const isModalOpen = ref(false)
const cart = ref([])
const showWarning = ref(false)

const handleSelectDish = (dish) => {
  selectedDish.value = dish
  isModalOpen.value = true
}

const handleAddToCart = (dish) => {
  if (cart.value.length >= 3) {
    showWarning.value = true
    setTimeout(() => {
      showWarning.value = false
    }, 3000)
    // Warning shown but proceed to add
  }

  // Prevent duplicates (optional, but good UX)
  if (!cart.value.find(d => d.id === dish.id)) {
    cart.value.push(dish)
  }
}

const removeFromCart = (dish) => {
  cart.value = cart.value.filter(d => d.id !== dish.id)
}

const generateMenu = () => {
  currentView.value = 'summary'
}

const closeModal = () => {
  isModalOpen.value = false
  setTimeout(() => {
    selectedDish.value = null
  }, 300)
}
</script>

<template>
  <div class="app-container">
    <nav class="pill-nav" v-if="currentView !== 'summary'">
      <button :class="{ active: currentView === 'random' }" @click="currentView = 'random'">
        ✨ 今天吃啥
      </button>
      <button :class="{ active: currentView === 'list' }" @click="currentView = 'list'">
        📋 全部菜单
      </button>
    </nav>

    <main class="content-area">
      <!-- Warning Toast -->
      <Transition name="fade">
        <div v-if="showWarning" class="warning-toast">
          😓 就这些吧，兰某做的慢，选多了做不完。
        </div>
      </Transition>

      <Transition name="fade" mode="out-in">
        <RandomSelector v-if="currentView === 'random'" :dishes="dishes" :cart="cart" @select="handleSelectDish"
          @addToCart="handleAddToCart" />
        <MenuList v-else-if="currentView === 'list'" :dishes="dishes" @select="handleSelectDish" />
        <div v-else-if="currentView === 'summary'" class="summary-view">
          <h2>🎉 今日菜单</h2>
          <div class="summary-list">
            <div v-for="dish in cart" :key="dish.id" class="summary-item">
              <span class="emoji">{{ dish.emoji }}</span>
              <div class="info">
                <h3>{{ dish.name }}</h3>
                <span class="tag">{{ dish.category }}</span>
              </div>
            </div>
          </div>
          <button class="back-btn" @click="currentView = 'random'">再选选</button>
        </div>
      </Transition>
    </main>

    <!-- Cart Overlay -->
    <MenuCart v-if="currentView !== 'summary'" :cart="cart" @remove="removeFromCart" @confirm="generateMenu" />

    <!-- Recipe Modal -->
    <RecipeModal v-if="selectedDish" :dish="selectedDish" :is-open="isModalOpen" @close="closeModal" />
  </div>
</template>

<style scoped>
.app-container {
  padding: 2rem 1rem;
  min-height: 100vh;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pill-nav {
  background: white;
  padding: 0.5rem;
  border-radius: 50px;
  display: flex;
  gap: 0.5rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
  margin-bottom: 2rem;
}

.pill-nav button {
  background: transparent;
  border: none;
  padding: 0.8rem 1.5rem;
  border-radius: 40px;
  font-size: 1rem;
  font-weight: 600;
  color: #8D6E63;
  cursor: pointer;
  transition: all 0.3s ease;
  font-family: inherit;
}

.pill-nav button.active {
  background: #FFAB91;
  color: #fff;
  box-shadow: 0 2px 10px rgba(255, 171, 145, 0.4);
}

.content-area {
  width: 100%;
}

/* Summary View */
.summary-view {
  text-align: center;
}

.summary-list {
  display: grid;
  gap: 1rem;
  margin: 2rem 0;
}

.summary-item {
  background: white;
  padding: 1.5rem;
  border-radius: 20px;
  display: flex;
  align-items: center;
  gap: 1rem;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
}

.summary-item .emoji {
  font-size: 3rem;
}

.summary-item h3 {
  margin: 0;
  font-size: 1.2rem;
  color: #5D4037;
}

.back-btn {
  background: white;
  color: #FF8A65;
  border: 2px solid #FFAB91;
  padding: 0.8rem 2rem;
  border-radius: 30px;
  font-family: inherit;
  font-size: 1rem;
}

/* Warnings */
.warning-toast {
  position: fixed;
  top: 20%;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.8);
  color: white;
  padding: 1rem 2rem;
  border-radius: 50px;
  z-index: 200;
  width: 80%;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
