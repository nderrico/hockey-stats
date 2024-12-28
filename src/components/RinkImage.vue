<template>
  <div class="rink-container">
    <div class="my-4 flex items-center justify-center gap-3 px-4">
      <template v-if="effectiveShootingDirection === 'left'">
        <svg class="w-6 h-6 md:w-8 md:h-8" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
        </svg>
        <span class="text-lg md:text-2xl">{{ homeTeam }} shoots</span>
      </template>
      <template v-else>
        <span class="text-lg md:text-2xl">{{ homeTeam }} shoots</span>
        <svg class="w-6 h-6 md:w-8 md:h-8" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3" />
        </svg>
      </template>
    </div>
    <div class="rink-image-container">
      <img 
        src="/images/hockey-rink.png"
        alt="Hockey Rink"
        @click="handleClick"
        :class="['hockey-rink', { 'cursor-not-allowed': !canRecordShots }]"
        ref="rinkImage"
      />
      <div 
        v-for="(shot, index) in shots" 
        :key="index" 
        :style="getShotStyle(shot)"
        class="shot-marker"
      >
        <span v-if="shot.isGoal">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" :style="{ stroke: shot.team === 'home' ? '#1e3a8a' : '#dc2626' }" width="24" height="24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </span>
        <span v-else :class="{'text-blue-900': shot.team === 'home', 'text-red-600': shot.team === 'away'}" class="shot-dot">•</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RinkImage',
  props: {
    shots: Array,
    isHome: Boolean,
    selectedPeriod: {
      type: [String, Number],
      required: true
    },
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
  computed: {
    canRecordShots() {
      return this.selectedPeriod !== 'All';
    },
    effectiveShootingDirection() {
      // Invert direction in period 2
      return this.selectedPeriod === 2 
        ? (this.shootingDirection === 'left' ? 'right' : 'left')
        : this.shootingDirection;
    }
  },
  methods: {
    handleClick(event) {
      if (this.canRecordShots) {
        this.$emit('recordShot', event);
      }
    },
    getShotStyle(shot) {
      return {
        left: `${shot.x}px`,
        top: `${shot.y}px`,
        position: 'absolute',
        fontSize: '16px',
      };
    }
  },
  mounted() {
    console.log('Shots prop:', this.shots); 
    console.log('Shots array:', this.shots); 
  }
};
</script>

<style scoped>
.rink-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  height: 100%;
  padding: 0 1rem;
  flex-grow: 1;
}

.rink-image-container {
  position: relative;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.hockey-rink {
  width: 100%;
  height: auto;
  max-height: calc(100vh - 200px);
  object-fit: contain;
}

.shot-marker {
  position: absolute;
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.shot-dot {
  font-size: 24px;
  line-height: 1;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

@media (min-width: 768px) {
  .shot-dot {
    font-size: 32px;
  }
}

@media (min-width: 1024px) {
  .shot-dot {
    font-size: 48px;
  }
}
</style>
