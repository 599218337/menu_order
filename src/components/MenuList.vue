<script setup>
import { computed } from 'vue'

const props = defineProps({
  dishes: {
    type: Array,
    required: true
  }
})

defineEmits(['select'])

const displayDishes = computed(() => {
  return props.dishes.filter(d => d.category !== '出去吃')
})
</script>

<template>
  <div class="menu-list">
    <div v-for="dish in displayDishes" :key="dish.id" class="menu-item" @click="$emit('select', dish)">
      <span class="item-emoji">{{ dish.emoji }}</span>
      <span class="item-name">{{ dish.name }}</span>
    </div>
  </div>
</template>

<style scoped>
.menu-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 1rem;
  padding: 1rem;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
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
