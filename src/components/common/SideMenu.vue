<template>
  <div class="side-menu" :style="{ backgroundColor: theme.backgroundColor }">
    <div class="menu-header">
      <Button
        icon="pi pi-arrow-left"
        class="back-button"
        rounded
        @click="$emit('back')"
        aria-label="Go back"
      />
      <h2 class="theme-title">{{ theme.name }}</h2>
    </div>

    <div class="menu-items">
      <div
        v-for="item in theme.items"
        :key="item.id"
        class="menu-item"
        :class="{ active: item.id === activeItemId }"
        @click="$emit('select', item.id)"
      >
        <div class="menu-item-image">
          <img :src="item.image" :alt="item.name" />
        </div>
        <div class="menu-item-text">
          <span class="item-name">{{ item.name }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Button from 'primevue/button'

defineProps({
  theme: {
    type: Object,
    required: true
  },
  activeItemId: {
    type: String,
    required: true
  }
})

defineEmits(['select', 'back'])
</script>

<style scoped>
.side-menu {
  height: 100%;
  display: flex;
  flex-direction: column;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.menu-header {
  padding: 24px;
  background: rgba(255, 255, 255, 0.3);
  display: flex;
  align-items: center;
  gap: 16px;
  border-bottom: 3px solid rgba(255, 255, 255, 0.5);
}

.back-button {
  flex-shrink: 0;
  width: 50px;
  height: 50px;
  font-size: 1.5rem;
  background: white;
  color: #333;
  border: 3px solid rgba(0, 0, 0, 0.1);
}

.back-button:hover {
  background: #f0f0f0;
  transform: scale(1.05);
}

.theme-title {
  margin: 0;
  font-size: 1.8rem;
  font-weight: bold;
  color: #333;
  flex: 1;
}

.menu-items {
  flex: 1;
  overflow-y: auto;
  padding: 16px;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  margin-bottom: 12px;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 16px;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 3px solid transparent;
}

.menu-item:hover {
  background: rgba(255, 255, 255, 0.8);
  transform: translateX(8px);
  border-color: white;
}

.menu-item.active {
  background: white;
  border-color: #333;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transform: translateX(8px);
}

.menu-item-image {
  width: 70px;
  height: 70px;
  border-radius: 12px;
  overflow: hidden;
  flex-shrink: 0;
  background: white;
  border: 2px solid rgba(0, 0, 0, 0.1);
}

.menu-item-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.menu-item-text {
  flex: 1;
}

.item-name {
  font-size: 1.4rem;
  font-weight: bold;
  color: #333;
  display: block;
}

/* Custom scrollbar */
.menu-items::-webkit-scrollbar {
  width: 8px;
}

.menu-items::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 10px;
}

.menu-items::-webkit-scrollbar-thumb {
  background: rgba(0, 0, 0, 0.2);
  border-radius: 10px;
}

.menu-items::-webkit-scrollbar-thumb:hover {
  background: rgba(0, 0, 0, 0.3);
}

@media (max-width: 768px) {
  .menu-header {
    padding: 16px;
  }

  .theme-title {
    font-size: 1.4rem;
  }

  .menu-item {
    padding: 12px;
  }

  .menu-item-image {
    width: 60px;
    height: 60px;
  }

  .item-name {
    font-size: 1.2rem;
  }
}
</style>
