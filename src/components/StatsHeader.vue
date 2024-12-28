<template>
  <div class="stats-header flex items-center bg-gray-300 p-4 relative">
    <!-- Hamburger Menu Button -->
    <button 
      @click="$emit('toggleMenu')"
      class="md:hidden text-gray-900 bg-white border border-gray-500 focus:outline-none hover:bg-gray-100 focus:ring-4 focus:ring-gray-100 font-medium rounded-lg text-sm px-3 py-2.5 absolute left-4"
      aria-label="Toggle menu"
    >
      <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
      </svg>
    </button>

    <div class="flex-1 flex justify-center items-center gap-4">
      <div class="away-stats text-right">
        <h2 class="font-bold text-lg">{{ awayTeam }}</h2>
        <div class="stats-grid">
          <div>
            <span class="text-gray-600">Shots:</span>
            <span class="font-semibold">{{ awayStats.totalShots }}</span>
          </div>
        </div>
      </div>
      <div>
        <span class="font-semibold text-5xl">{{ awayStats.goals }} - {{ homeStats.goals }}</span>
      </div>
      <div class="home-stats text-left">
        <div>
          <h2 class="font-bold text-lg">{{ homeTeam }}</h2>
          <div class="stats-grid">
            <span class="text-gray-600">Shots:</span>
            <span class="font-semibold">{{ homeStats.totalShots }}</span>
          </div>
        </div>
      </div>
    </div>
    <button 
      @click="showSettings = true"
      class="text-gray-900 bg-white border border-gray-500 focus:outline-none hover:bg-gray-100 focus:ring-4 focus:ring-gray-100 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 absolute right-4"
    >
      <svg class="w-6 h-6" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4"/>
      </svg>
    </button>
  </div>

  <SettingsModal
    :show="showSettings"
    :home-team="homeTeam"
    :away-team="awayTeam"
    :shooting-direction="shootingDirection"
    @update:homeTeam="$emit('update:homeTeam', $event)"
    @update:awayTeam="$emit('update:awayTeam', $event)"
    @update:shootingDirection="$emit('update:shootingDirection', $event)"
    @close="showSettings = false"
  />
</template>

<script>
import SettingsModal from './SettingsModal.vue';

export default {
  components: {
    SettingsModal
  },
  props: {
    homeStats: Object,
    awayStats: Object,
    homeTeam: {
      type: String,
      default: 'Home'
    },
    awayTeam: {
      type: String,
      default: 'Away'
    },
    shootingDirection: {
      type: String,
      default: 'left'
    }
  },
  data() {
    return {
      showSettings: false
    }
  },
  emits: ['update:homeTeam', 'update:awayTeam', 'update:shootingDirection', 'toggleMenu']
};
</script>

<style scoped>
.stats-header {
  position: sticky;
  top: 0;
  z-index: 10;
  width: 100%;
}

@media (max-width: 768px) {
  .stats-grid {
    font-size: 0.875rem;
  }
  
  .font-semibold.text-5xl {
    font-size: 2rem;
  }
  
  .font-bold.text-lg {
    font-size: 1rem;
  }
}
</style>