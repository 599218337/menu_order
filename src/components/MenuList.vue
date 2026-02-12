<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  dishes: {
    type: Array,
    required: true
  }
})

defineEmits(['select'])

const selectedCategory = ref('全部')
const categories = ['全部', '主食', '小炒', '大菜', '汤', '凉菜', '甜品',]

const displayDishes = computed(() => {
  let list = props.dishes.filter(d => d.category !== '出去吃')

  if (selectedCategory.value !== '全部') {
    list = list.filter(d => d.category === selectedCategory.value)
  }

  return list
})
</script>

<template>
  <div class="menu-container">
    <div class="categories">
      <button v-for="cat in categories" :key="cat" :class="{ active: selectedCategory === cat }"
        @click="selectedCategory = cat">
        {{ cat }}
      </button>
    </div>

    <div class="menu-list">
      <div v-for="dish in displayDishes" :key="dish.id" class="menu-item" @click="$emit('select', dish)">
        <span class="item-emoji">{{ dish.emoji }}</span>
        <span class="item-name">{{ dish.name }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.menu-container {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.categories {
  display: flex;
  gap: 0.5rem;
  overflow-x: auto;
  padding: 0.5rem;
  margin-bottom: 1rem;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  justify-content: center;
}

.categories::-webkit-scrollbar {
  display: none;
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
  cursor: pointer;
  transition: all 0.2s;
}

.categories button.active {
  background: #FF8A65;
  color: white;
}

.menu-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 1rem;
  padding: 0 1rem 5rem 1rem;
  /* Added bottom padding for space */
}

.menu-item {
  background: white;
  padding: 1.5rem;
  border-radius: 16px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  transition: transform 0.2s;
  cursor: pointer;
}

.menu-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
}

.item-emoji {
  font-size: 2.5rem;
}

.item-name {
  font-weight: 500;
  color: #5D4037;
  font-size: 1rem;
}
</style>
