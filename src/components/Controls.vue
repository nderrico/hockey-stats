<template>
  <SlideoutMenu 
    :modelValue="isMenuOpen"
    @update:modelValue="$emit('update:isMenuOpen', $event)"
  >
    <div class="h-full flex items-start p-4">
      <div class="w-full flex flex-col md:flex-row md:items-center md:justify-between space-y-6 md:space-y-0" :class="{ 'opacity-50': selectedPeriod === 'All' }">
        <!-- Left Controls -->
        <div class="flex flex-col md:flex-row md:items-center space-y-4 md:space-y-0 md:space-x-2">
          <!-- Shot/Goal Toggle -->
          <div class="inline-flex items-center" role="group">
            <button 
              @click="selectedPeriod !== 'All' && $emit('update:isGoal', false)"
              :disabled="selectedPeriod === 'All'"
              :class="[
                'h-[38px] px-4 text-sm font-medium border rounded-s-lg',
                'focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700',
                !isGoal 
                  ? 'bg-blue-50 text-blue-700 border-gray-200'
                  : 'bg-white text-gray-900 border-gray-200 hover:bg-gray-100 hover:text-blue-700'
              ]"
            >
              Shot
            </button>
            <button 
              @click="selectedPeriod !== 'All' && $emit('update:isGoal', true)"
              :disabled="selectedPeriod === 'All'"
              :class="[
                'h-[38px] px-4 text-sm font-medium border rounded-e-lg',
                'focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700',
                isGoal 
                  ? 'bg-blue-50 text-blue-700 border-gray-200'
                  : 'bg-white text-gray-900 border-gray-200 hover:bg-gray-100 hover:text-blue-700'
              ]"
            >
              Goal
            </button>
          </div>

          <!-- Home/Away Toggle -->
          <div class="inline-flex items-center" role="group">
            <button 
              @click="selectedPeriod !== 'All' && $emit('update:isHome', true)"
              :disabled="selectedPeriod === 'All'"
              :class="[
                'h-[38px] px-4 text-sm font-medium border rounded-e-lg',
                'focus:z-10 focus:ring-2 focus:ring-blue-700',
                isHome 
                  ? 'bg-blue-900 text-white border-blue-900'
                  : 'bg-white text-gray-900 border-gray-200 hover:bg-gray-100'
              ]"
            >
              {{ homeTeam }}
            </button>
            <button 
              @click="selectedPeriod !== 'All' && $emit('update:isHome', false)"
              :disabled="selectedPeriod === 'All'"
              :class="[
                'h-[38px] px-4 text-sm font-medium border rounded-s-lg',
                'focus:z-10 focus:ring-2 focus:ring-blue-700',
                !isHome 
                  ? 'bg-red-600 text-white border-red-600'
                  : 'bg-white text-gray-900 border-gray-200 hover:bg-gray-100'
              ]"
            >
              {{ awayTeam }}
            </button>
          </div>
        </div>

        <!-- Period Controls -->
        <div class="flex">
          <div class="inline-flex items-center md:w-auto" role="group">
            <button 
              v-for="period in [1, 2, 3, 'All']" 
              :key="period"
              @click="$emit('update:selectedPeriod', period)"
              :class="[
                'h-[38px] flex-1 md:flex-none px-4 text-sm font-medium border',
                'hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700',
                selectedPeriod === period 
                  ? 'text-blue-700 bg-blue-50'
                  : 'text-gray-900 bg-white',
                period === 1 ? 'rounded-s-lg border-gray-200' : '',
                period === 'All' ? 'rounded-e-lg border-gray-200' : '',
                period !== 1 && period !== 'All' ? 'border-t border-b border-gray-200' : ''
              ]"
            >
              {{ period === 'All' ? 'Game' : period }}
            </button>
          </div>
        </div>

        <!-- Action Buttons -->
        <div class="flex flex-col md:flex-row justify-end space-y-4 md:space-y-0 md:space-x-2">
          <button 
            @click="undoLastShot"
            class="h-[38px] focus:outline-none text-white bg-yellow-600 hover:bg-yellow-700 focus:ring-4 focus:ring-yellow-300 font-medium rounded-lg text-sm px-4 flex items-center justify-center"
          >
            <svg class="w-5 h-5 text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
              <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9h13a5 5 0 0 1 0 10H7M3 9l4-4M3 9l4 4"/>
            </svg>
          </button>
          <button 
            @click="showClearModal = true"
            class="h-[38px] focus:outline-none text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-4 flex items-center justify-center"
          >
            <svg class="w-5 h-5 text-white mr-2" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
              <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/>
            </svg>
            <span class="font-semibold">Clear</span>
          </button>
        </div>
      </div>
    </div>
  </SlideoutMenu>

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
import SlideoutMenu from './SlideoutMenu.vue';

export default {
  components: {
    ConfirmationModal,
    SlideoutMenu
  },
  props: {
    isGoal: Boolean,
    isHome: Boolean,
    selectedPeriod: [String, Number],
    homeTeam: String,
    awayTeam: String,
    undoLastShot: Function,
    clearShots: Function,
    isMenuOpen: {
      type: Boolean,
      default: false
    }
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
  emits: ['update:isGoal', 'update:isHome', 'update:selectedPeriod', 'update:isMenuOpen']
};
</script>

<style scoped>
.controls-content {
  padding: 1rem;
}

.shot-controls {
  margin-bottom: 1rem;
}

.period-controls {
  margin-bottom: 1rem;
}

.action-buttons {
  margin-bottom: 1rem;
}
</style>
