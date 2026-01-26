<script setup>
defineProps({
    cart: {
        type: Array,
        required: true
    }
})

defineEmits(['remove', 'confirm'])
</script>

<template>
    <div class="menu-cart" v-if="cart.length > 0">
        <div class="cart-header">
            <h3>已选菜单 ({{ cart.length }})</h3>
            <button class="confirm-btn" @click="$emit('confirm')">生成菜单吧</button>
        </div>
        <div class="cart-items">
            <div v-for="dish in cart" :key="dish.id" class="cart-item">
                <span class="cart-emoji">{{ dish.emoji }}</span>
                <span class="cart-name">{{ dish.name }}</span>
                <button class="remove-btn" @click="$emit('remove', dish)">×</button>
            </div>
        </div>
    </div>
</template>

<style scoped>
.menu-cart {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    background: white;
    box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.1);
    padding: 1rem;
    border-radius: 20px 20px 0 0;
    z-index: 100;
    transform: translateY(0);
    transition: transform 0.3s ease;
    max-height: 40vh;
    overflow-y: auto;
}

.cart-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1rem;
}

h3 {
    margin: 0;
    color: #5D4037;
    font-size: 1.2rem;
}

.confirm-btn {
    background: #FF8A65;
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 20px;
    font-family: inherit;
    font-weight: bold;
    cursor: pointer;
}

.cart-items {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
}

.cart-item {
    background: #FFF8E1;
    padding: 0.5rem 0.8rem;
    border-radius: 15px;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.9rem;
}

.remove-btn {
    background: none;
    border: none;
    color: #8D6E63;
    font-size: 1.2rem;
    cursor: pointer;
    padding: 0;
    line-height: 1;
}
</style>
