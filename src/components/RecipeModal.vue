<script setup>
defineProps({
    dish: {
        type: Object,
        required: true
    },
    isOpen: {
        type: Boolean,
        required: true
    }
})

defineEmits(['close'])
</script>

<template>
    <Transition name="modal">
        <div v-if="isOpen" class="modal-overlay" @click="$emit('close')">
            <div class="modal-content" @click.stop>
                <button class="close-btn" @click="$emit('close')">×</button>

                <div class="modal-header">
                    <span class="emoji">{{ dish.emoji }}</span>
                    <h2>{{ dish.name }}</h2>
                </div>

                <div class="modal-body">
                    <div class="section">
                        <h3>🛒 准备食材</h3>
                        <ul>
                            <li v-for="(ingredient, index) in dish.ingredients" :key="index">
                                {{ ingredient }}
                            </li>
                        </ul>
                    </div>

                    <div class="section">
                        <h3>🍳 做法步骤</h3>
                        <ol>
                            <li v-for="(step, index) in dish.steps" :key="index">
                                {{ step }}
                            </li>
                        </ol>
                    </div>
                </div>
            </div>
        </div>
    </Transition>
</template>

<style scoped>
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: flex-end;
    /* Mobile bottom sheet style initially suitable */
    z-index: 1000;
    backdrop-filter: blur(4px);
}

.modal-content {
    background: #FFF8E1;
    width: 100%;
    max-width: 600px;
    height: 85vh;
    /* Take up most of the screen */
    border-radius: 24px 24px 0 0;
    padding: 2rem;
    overflow-y: auto;
    position: relative;
    box-shadow: 0 -10px 40px rgba(0, 0, 0, 0.2);
    animation: slideUp 0.3s ease-out;
}

@media (min-width: 600px) {
    .modal-overlay {
        align-items: center;
        padding: 2rem;
    }

    .modal-content {
        height: auto;
        max-height: 80vh;
        border-radius: 24px;
        animation: zoomIn 0.3s ease-out;
    }
}

.close-btn {
    position: absolute;
    top: 1rem;
    right: 1.5rem;
    background: none;
    border: none;
    font-size: 2rem;
    color: #8D6E63;
    cursor: pointer;
    z-index: 10;
}

.modal-header {
    text-align: center;
    margin-bottom: 2rem;
}

.emoji {
    font-size: 4rem;
    display: block;
    margin-bottom: 0.5rem;
}

h2 {
    color: #5D4037;
    font-size: 2rem;
    margin: 0;
}

h3 {
    color: #FF8A65;
    border-bottom: 2px solid #FFAB91;
    padding-bottom: 0.5rem;
    margin-top: 1.5rem;
}

ul,
ol {
    padding-left: 1.5rem;
    color: #5D4037;
    line-height: 1.8;
}

li {
    margin-bottom: 0.5rem;
}

/* Animations */
.modal-enter-active,
.modal-leave-active {
    transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
    opacity: 0;
}

@keyframes slideUp {
    from {
        transform: translateY(100%);
    }

    to {
        transform: translateY(0);
    }
}

@keyframes zoomIn {
    from {
        transform: scale(0.9);
        opacity: 0;
    }

    to {
        transform: scale(1);
        opacity: 1;
    }
}
</style>
