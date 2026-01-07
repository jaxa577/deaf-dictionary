<template>
  <div v-if="theme" class="theme-detail-page">
    <div class="content-wrapper">
      <!-- Left Menu -->
      <aside class="sidebar">
        <SideMenu
          :theme="theme"
          :active-item-id="activeItemId"
          @select="selectItem"
          @back="goBack"
        />
      </aside>

      <!-- Main Content -->
      <main class="main-content">
        <div v-if="activeItem" class="item-display">
          <!-- Item Name -->
          <div class="item-header">
            <h1 class="item-name">{{ activeItem.name }}</h1>
            <p v-if="activeItem.description" class="item-description">
              {{ activeItem.description }}
            </p>
          </div>

          <!-- Item Image -->
          <div class="item-image-container">
            <img
              :src="activeItem.image"
              :alt="activeItem.name"
              class="item-image"
            />
          </div>

          <!-- Sign Language Video -->
          <VideoPlayer
            :video-src="activeItem.signVideo"
            :gif-src="activeItem.signGif"
            :alt="`Sign language for ${activeItem.name}`"
            :caption="`Sign: ${activeItem.name}`"
          />

          <!-- Navigation Buttons -->
          <div class="navigation-buttons">
            <Button
              icon="pi pi-arrow-left"
              label="Previous"
              class="nav-button prev-button"
              :disabled="!hasPrevious"
              @click="goToPrevious"
              size="large"
            />
            <Button
              icon="pi pi-arrow-right"
              iconPos="right"
              label="Next"
              class="nav-button next-button"
              :disabled="!hasNext"
              @click="goToNext"
              size="large"
            />
          </div>
        </div>

        <div v-else class="no-item">
          <i class="pi pi-info-circle"></i>
          <p>Select an item from the menu</p>
        </div>
      </main>
    </div>
  </div>

  <div v-else class="loading">
    <ProgressSpinner />
    <p>Loading theme...</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import Button from 'primevue/button'
import ProgressSpinner from 'primevue/progressspinner'
import SideMenu from '@/components/common/SideMenu.vue'
import VideoPlayer from '@/components/common/VideoPlayer.vue'
import { getThemeById } from '@/data/themes'

const route = useRoute()
const router = useRouter()

const theme = ref(null)
const activeItemId = ref('')

// Load theme data
const loadTheme = () => {
  const themeId = route.params.themeId
  theme.value = getThemeById(themeId)

  if (theme.value && theme.value.items.length > 0) {
    // Set first item as active by default, or use query param
    activeItemId.value = route.query.itemId || theme.value.items[0].id
  }
}

onMounted(() => {
  loadTheme()
})

watch(() => route.params.themeId, () => {
  loadTheme()
})

// Active item
const activeItem = computed(() => {
  return theme.value?.items.find(item => item.id === activeItemId.value)
})

// Current index
const currentIndex = computed(() => {
  return theme.value?.items.findIndex(item => item.id === activeItemId.value) ?? -1
})

// Navigation
const hasPrevious = computed(() => currentIndex.value > 0)
const hasNext = computed(() => {
  return theme.value && currentIndex.value < theme.value.items.length - 1
})

const selectItem = (itemId) => {
  activeItemId.value = itemId
  router.replace({ query: { itemId } })
}

const goToPrevious = () => {
  if (hasPrevious.value) {
    const prevItem = theme.value.items[currentIndex.value - 1]
    selectItem(prevItem.id)
  }
}

const goToNext = () => {
  if (hasNext.value) {
    const nextItem = theme.value.items[currentIndex.value + 1]
    selectItem(nextItem.id)
  }
}

const goBack = () => {
  router.push({ name: 'home' })
}
</script>

<style scoped>
.theme-detail-page {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
}

.content-wrapper {
  max-width: 1600px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 320px 1fr;
  gap: 24px;
  min-height: calc(100vh - 40px);
}

.sidebar {
  position: sticky;
  top: 20px;
  height: fit-content;
  max-height: calc(100vh - 40px);
}

.main-content {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: 40px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.item-display {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 32px;
  animation: fadeIn 0.5s ease-out;
}

.item-header {
  text-align: center;
  width: 100%;
}

.item-name {
  font-size: 3.5rem;
  font-weight: bold;
  color: #333;
  margin: 0 0 16px 0;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.item-description {
  font-size: 1.4rem;
  color: #666;
  margin: 0;
  line-height: 1.6;
}

.item-image-container {
  width: 100%;
  max-width: 500px;
  aspect-ratio: 1;
  border-radius: 24px;
  overflow: hidden;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.15);
  border: 6px solid white;
}

.item-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.navigation-buttons {
  display: flex;
  gap: 24px;
  margin-top: 24px;
}

.nav-button {
  font-size: 1.4rem;
  padding: 16px 32px;
  border-radius: 16px;
  border: none;
  transition: all 0.3s ease;
}

.prev-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.next-button {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

.nav-button:hover:not(:disabled) {
  transform: translateY(-4px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
}

.nav-button:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.no-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 400px;
  color: #999;
  font-size: 1.5rem;
}

.no-item i {
  font-size: 4rem;
  margin-bottom: 16px;
}

.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  color: white;
  font-size: 1.5rem;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 1024px) {
  .content-wrapper {
    grid-template-columns: 1fr;
    gap: 16px;
  }

  .sidebar {
    position: static;
    max-height: none;
  }

  .main-content {
    padding: 24px;
  }

  .item-name {
    font-size: 2.5rem;
  }

  .navigation-buttons {
    flex-direction: column;
    width: 100%;
  }

  .nav-button {
    width: 100%;
  }
}

@media (max-width: 768px) {
  .theme-detail-page {
    padding: 12px;
  }

  .main-content {
    padding: 20px;
  }

  .item-name {
    font-size: 2rem;
  }

  .item-description {
    font-size: 1.2rem;
  }
}
</style>
