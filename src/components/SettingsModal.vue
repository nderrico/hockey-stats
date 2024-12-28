<template>
  <div v-if="show" class="fixed inset-0 z-[1100] overflow-y-auto">
    <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
      <div class="relative transform overflow-hidden rounded-lg border border-gray-300 text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg">
        <div class="bg-gray-100 px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
          <div class="sm:flex sm:items-start">
            <div class="mt-3 text-center sm:ml-4 sm:mt-0 sm:text-left w-full">
              <h3 class="text-xl font-semibold leading-6 text-gray-900 mb-4">Settings</h3>
              <div class="mt-2">
                <div class="mb-4">
                  <label class="block mb-2 text-sm font-medium text-gray-900">Home Team Name</label>
                  <input 
                    type="text" 
                    v-model="localHomeTeam" 
                    class="shadow-sm bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                  >
                </div>
                <div class="mb-4">
                  <label class="block mb-2 text-sm font-medium text-gray-900">Away Team Name</label>
                  <input 
                    type="text" 
                    v-model="localAwayTeam" 
                    class="shadow-sm bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
                  >
                </div>
                <div class="mb-4">
                  <label class="block mb-2 text-sm font-medium text-gray-900">{{ localHomeTeam }} shoots</label>
                  <div class="inline-flex rounded-md shadow-sm" role="group">
                    <button 
                      @click="localShootingDirection = 'left'"
                      :class="[
                        'px-4 py-2 text-sm font-medium border',
                        'hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700',
                        localShootingDirection === 'left'
                          ? 'text-blue-700 bg-blue-50'
                          : 'text-gray-900 bg-white',
                        'rounded-s-lg border-gray-200'
                      ]"
                    >
                      Left
                    </button>
                    <button 
                      @click="localShootingDirection = 'right'"
                      :class="[
                        'px-4 py-2 text-sm font-medium border border-gray-200',
                        'hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700',
                        localShootingDirection === 'right'
                          ? 'text-blue-700 bg-blue-50'
                          : 'text-gray-900 bg-white',
                        'rounded-e-lg'
                      ]"
                    >
                      Right
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="bg-gray-200 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6">
          <button 
            type="button" 
            @click="saveSettings"
            class="inline-flex w-full justify-center rounded-md bg-blue-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-500 sm:ml-3 sm:w-auto"
          >
            Save Changes
          </button>
          <button 
            type="button" 
            @click="$emit('close')"
            class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto"
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: Boolean,
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
      localHomeTeam: this.homeTeam,
      localAwayTeam: this.awayTeam,
      localShootingDirection: this.shootingDirection
    }
  },
  watch: {
    show(newVal) {
      if (newVal) {
        this.localHomeTeam = this.homeTeam;
        this.localAwayTeam = this.awayTeam;
        this.localShootingDirection = this.shootingDirection;
      }
    }
  },
  methods: {
    saveSettings() {
      this.$emit('update:homeTeam', this.localHomeTeam);
      this.$emit('update:awayTeam', this.localAwayTeam);
      this.$emit('update:shootingDirection', this.localShootingDirection);
      this.$emit('close');
    }
  }
}
</script>
