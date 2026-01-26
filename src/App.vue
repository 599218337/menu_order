<script setup>
import { ref } from 'vue'
import dishesData from './assets/dishes.json'
import RandomSelector from './components/RandomSelector.vue'
import MenuList from './components/MenuList.vue'
import RecipeModal from './components/RecipeModal.vue'

const dishes = ref(dishesData)
const currentView = ref('random') // 'random' or 'list'
const selectedDish = ref(null)
const isModalOpen = ref(false)

const handleSelectDish = (dish) => {
  selectedDish.value = dish
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
  setTimeout(() => {
    selectedDish.value = null
  }, 300) // Clear after animation
}
</script>

<template>
  <div class="app-container">
    <nav class="pill-nav">
      <button :class="{ active: currentView === 'random' }" @click="currentView = 'random'">
        ✨ 今天吃啥
      </button>
      <button :class="{ active: currentView === 'list' }" @click="currentView = 'list'">
        📋 全部菜单
      </button>
    </nav>

    <main class="content-area">
      <Transition name="fade" mode="out-in">
        <RandomSelector v-if="currentView === 'random'" :dishes="dishes" @select="handleSelectDish" />
        <MenuList v-else :dishes="dishes" @select="handleSelectDish" />
      </Transition>
    </main>

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
  /* Inherit new font */
}

.pill-nav button.active {
  background: #FFAB91;
  color: #fff;
  box-shadow: 0 2px 10px rgba(255, 171, 145, 0.4);
}

.content-area {
  width: 100%;
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
