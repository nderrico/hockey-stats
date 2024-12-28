<template>
  <div class="content-area">
    <div class="header-container">
      <StatsHeader 
        :homeStats="homeStats" 
        :awayStats="awayStats" 
        :home-team="homeTeam" 
        :away-team="awayTeam"
        @update:homeTeam="homeTeam = $event"
        @update:awayTeam="awayTeam = $event"
        :shooting-direction="shootingDirection"
        @update:shootingDirection="shootingDirection = $event"
        @toggleMenu="isMenuOpen = !isMenuOpen"
      />
    </div>
    <Controls 
      :isGoal="isGoal" 
      :isHome="isHome" 
      :selectedPeriod="selectedPeriod"
      :home-team="homeTeam"
      :away-team="awayTeam"
      @update:isGoal="isGoal = $event" 
      @update:isHome="isHome = $event" 
      @update:selectedPeriod="selectedPeriod = $event"
      :undoLastShot="undoLastShot" 
      :clearShots="clearShots" 
      v-model:isMenuOpen="isMenuOpen"
    />
    <RinkImage 
      :shots="filteredShots" 
      :selectedPeriod="selectedPeriod"
      :isHome="isHome"
      :home-team="homeTeam"
      :away-team="awayTeam"
      :shooting-direction="shootingDirection"
      @recordShot="recordShot" 
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import StatsHeader from './StatsHeader.vue';
import Controls from './Controls.vue';
import RinkImage from './RinkImage.vue';

const shots = ref([]);
const isGoal = ref(false);
const isHome = ref(true);
const selectedPeriod = ref(1);
const homeTeam = ref('Home');
const awayTeam = ref('Away');
const shootingDirection = ref('left');
const isMenuOpen = ref(false);

const filteredShots = computed(() => {
  if (selectedPeriod.value === 'All') {
    return shots.value.map(shot => {
      if (shot.period === 2) {
        // Flip X coordinates for period 2 shots when viewing all periods
        return {
          ...shot,
          x: shot.x !== undefined ? (shot.rinkWidth - shot.x) : shot.x
        };
      }
      return shot;
    });
  }
  return shots.value.filter(shot => shot.period === selectedPeriod.value);
});

const homeStats = computed(() => {
  const homeShots = filteredShots.value.filter(shot => shot.team === 'home'); 
  return {
    goals: homeShots.filter(shot => shot.isGoal).length,
    totalShots: homeShots.length
  }
})

const awayStats = computed(() => {
  const awayShots = filteredShots.value.filter(shot => shot.team === 'away'); 
  return {
    goals: awayShots.filter(shot => shot.isGoal).length,
    totalShots: awayShots.length
  }
})

function saveData() {
  const data = { 
    shots: shots.value,
    homeTeam: homeTeam.value,
    awayTeam: awayTeam.value,
    shootingDirection: shootingDirection.value
  };
  localStorage.setItem('myData', JSON.stringify(data));
  console.log('Saved data to local storage:', JSON.parse(localStorage.getItem('myData'))); 
}

function loadData() {
  const data = localStorage.getItem('myData');
  console.log('Loaded data from local storage:', data); 
  return data ? JSON.parse(data) : null;
}

function updateData(newShots) {
  shots.value = newShots;
  saveData();
}

onMounted(() => {
  const storedData = loadData();
  if (storedData) {
    shots.value = storedData.shots;
    if (storedData.homeTeam) homeTeam.value = storedData.homeTeam;
    if (storedData.awayTeam) awayTeam.value = storedData.awayTeam;
    if (storedData.shootingDirection) shootingDirection.value = storedData.shootingDirection;
    console.log('Data loaded:', storedData); 
  }
});

const clearShots = () => {
  console.log('Clear Shots called'); 
  shots.value = [];
  saveData();
}

const undoLastShot = () => {
  console.log('Undo Last Shot called'); 
  shots.value.pop();
  saveData();
}

const recordShot = (event) => {
  const rect = event.target.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;
  const rinkWidth = rect.width; // Store rink width for coordinate flipping

  const shot = {
    x,
    y,
    rinkWidth,
    team: isHome.value ? 'home' : 'away',
    isGoal: isGoal.value,
    period: selectedPeriod.value === 'All' ? 1 : selectedPeriod.value,
    timestamp: new Date().toISOString()
  };

  shots.value.push(shot);
  saveData();
};

const getShotStyle = (shot) => {
  return {
    left: `${shot.x}px`,
    top: `${shot.y}px`,
    position: 'absolute',
    transform: 'translate(-50%, -50%)'
  }
}
</script>

<style scoped>
.content-area {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.header-container {
  position: sticky;
  top: 0;
  z-index: 51;
  background-color: white;
}
</style>
