<template>
  <div class="relative">
    <!-- Overlay -->
    <div 
      v-show="modelValue" 
      class="fixed inset-0 bg-black bg-opacity-50 z-[998] md:hidden"
      @click="$emit('update:modelValue', false)"
    ></div>

    <!-- Slideout Content -->
    <div 
      :class="[
        'fixed md:relative',
        'top-0 left-0 bottom-0 md:inset-auto',
        'w-[300px] md:w-full',
        'bg-gray-100',
        'z-[999]',
        'overflow-y-auto md:overflow-visible',
        'pt-16 md:pt-0',
        'shadow-lg md:shadow-none',
        'transition-transform duration-300',
        modelValue ? 'translate-x-0' : '-translate-x-full md:translate-x-0'
      ]"
    >
      <!-- Close Button -->
      <button 
        @click="$emit('update:modelValue', false)"
        class="absolute top-4 right-4 md:hidden bg-transparent"
        aria-label="Close menu"
      >
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M6 18L18 6M6 6l12 12"/>
        </svg>
      </button>

      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SlideoutMenu',
  props: {
    modelValue: {
      type: Boolean,
      default: false
    }
  },
  emits: ['update:modelValue']
};
</script>

<style scoped>
.slideout-wrapper {
  position: relative;
  z-index: 50;
}

.slideout-container {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  width: 300px;
  z-index: 41;
  overflow-y: auto;
  padding-top: 4rem; /* Add space for the header */
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.1);
}

@media (min-width: 768px) {
  .slideout-container {
    position: relative;
    width: 100%;
    height: auto;
    overflow: visible;
    padding-top: 0;
    box-shadow: none;
    transform: none !important;
  }
}
</style>
