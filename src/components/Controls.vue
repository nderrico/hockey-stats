<template>
  <div class="controls-container mb-4 flex justify-between items-center bg-gray-100 p-4 rounded-b-lg">
    <div class="shot-controls flex space-x-8 items-center" :class="{ 'opacity-50': selectedPeriod === 'All' }">
      <label class="inline-flex items-center" :class="{ 'cursor-not-allowed': selectedPeriod === 'All', 'cursor-pointer': selectedPeriod !== 'All' }">
        <span :class="{'text-black': !isGoal, 'text-gray-400': isGoal}" class="mr-2">Shot</span>
        <input 
          type="checkbox" 
          :checked="isGoal" 
          @change="selectedPeriod !== 'All' && $emit('update:isGoal', !isGoal)" 
          class="sr-only peer" 
          :disabled="selectedPeriod === 'All'"
        />
        <div class="relative w-11 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-blue-300 rounded-full peer peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:start-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-blue-600"></div>
        <span :class="{'text-black': isGoal, 'text-gray-400': !isGoal}" class="ml-2">Goal</span>
      </label>
      <label class="inline-flex items-center" :class="{ 'cursor-not-allowed': selectedPeriod === 'All', 'cursor-pointer': selectedPeriod !== 'All' }">
        <span :class="{'text-black': !isHome, 'text-gray-400': isHome}" class="mr-2">{{ awayTeam }}</span>
        <input 
          type="checkbox" 
          :checked="isHome" 
          @change="selectedPeriod !== 'All' && $emit('update:isHome', !isHome)" 
          class="sr-only peer" 
          :disabled="selectedPeriod === 'All'"
        />
        <div class="relative w-11 h-6 bg-red-600 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-blue-300 rounded-full peer peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:start-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-blue-900"></div>
        <span :class="{'text-black': isHome, 'text-gray-400': !isHome}" class="ml-2">{{ homeTeam }}</span>
      </label>
    </div>
    <div class="inline-flex rounded-md shadow-sm" role="group">
      <button 
        v-for="period in [1, 2, 3, 'All']" 
        :key="period"
        @click="$emit('update:selectedPeriod', period)"
        :class="[
          'px-4 py-2 text-sm font-medium border',
          'hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700',
          selectedPeriod === period 
            ? 'text-blue-700 bg-blue-50'
            : 'text-gray-900 bg-white',
          period === 1 ? 'rounded-s-lg border-gray-200' : '',
          period === 'All' ? 'rounded-e-lg border-gray-200' : '',
          period !== 1 && period !== 'All' ? 'border-t border-b border-gray-200' : ''
        ]"
      >
        {{ period === 'All' ? 'Whole Game' : `Period ${period}` }}
      </button>
    </div>
    <div class="action-buttons flex space-x-4">
      <button 
        @click="undoLastShot"
        class="focus:outline-none text-white bg-yellow-600 hover:bg-yellow-700 focus:ring-4 focus:ring-yellow-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 flex items-center"
      >
        <svg class="w-5 h-5 text-white mr-2" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
          <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9h13a5 5 0 0 1 0 10H7M3 9l4-4M3 9l4 4"/>
        </svg>
        <span class="font-semibold">Undo</span>
      </button>
      <button 
        @click="showClearModal = true"
        class="focus:outline-none text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 flex items-center"
      >
        <svg class="w-5 h-5 text-white mr-2" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
          <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/>
        </svg>
        <span class="font-semibold">Clear All</span>
      </button>
    </div>
  </div>

  <ConfirmationModal
    :show="showClearModal"
    title="Clear All Shots"
    message="Are you sure you want to clear all shots? This cannot be undone."
    confirm-button-text="Clear Shots"
    @confirm="confirmClear"
    @cancel="showClearModal = false"
  />
</template>

<script>
import ConfirmationModal from './ConfirmationModal.vue';

export default {
  components: {
    ConfirmationModal
  },
  props: {
    isGoal: Boolean,
    isHome: Boolean,
    selectedPeriod: {
      type: [Number, String],
      default: 1
    },
    homeTeam: {
      type: String,
      default: 'Home'
    },
    awayTeam: {
      type: String,
      default: 'Away'
    },
    undoLastShot: Function,
    clearShots: Function
  },
  data() {
    return {
      showClearModal: false
    }
  },
  methods: {
    confirmClear() {
      this.clearShots();
      this.showClearModal = false;
    }
  },
  emits: ['update:isGoal', 'update:isHome', 'update:selectedPeriod']
};
</script>

<style scoped>
/* Add any specific styles here */
</style>
