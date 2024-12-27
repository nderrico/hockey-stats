<template>
  <div class="rink-container" style="display: flex; justify-content: center; flex-direction: column; align-items: center;">
    <div class="mb-4 flex items-center gap-3">
      <template v-if="effectiveShootingDirection === 'left'">
        <svg class="w-8 h-8" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
        </svg>
        <span class="text-2xl">{{ homeTeam }} shoots</span>
      </template>
      <template v-else>
        <span class="text-2xl">{{ homeTeam }} shoots</span>
        <svg class="w-8 h-8" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3" />
        </svg>
      </template>
    </div>
    <div class="relative">
      <img 
        src="/images/hockey-rink.png"
        alt="Hockey Rink"
        style="width: auto; max-width: 100%; height: auto;"
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
        <span v-if="shot.isGoal" class="shot-icon">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" :style="{ stroke: shot.team === 'home' ? '#000080' : '#FF0000' }" width="24" height="24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </span>
        <span v-else :class="{'text-navy-600': shot.team === 'home', 'text-red-600': shot.team === 'away'}" style="font-size: 48px; position: absolute; left: 50%; top: 50%; transform: translate(-50%, -50%);">•</span>
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
  position: relative;
  display: inline-block;
}

.shot-marker {
  position: absolute;
  transform: translate(-50%, -50%);
  z-index: 10;
}

.shot-icon {
  display: block;
  position: relative;
}

.cursor-not-allowed {
  cursor: not-allowed;
}
</style>
