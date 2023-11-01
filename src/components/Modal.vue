<template>
  <Transition name="modal-outer" >
      <div
        v-show="modalActive"
        class="absolute z-50 w-full bg-black bg-opacity-50 h-screen top-0 left-0 flex justify-center px-8"
      >
        <Transition name="modal-inner">
            <div
              v-if="modalActive"
              class="p-5 rounded-xl bg-[#EEEEEE] self-start mt-36 max-w-screen-md"
            >
              <slot />
              <button
                @click="$emit('close')"
                class="text-[#393E46] font-mono rounded-lg font-bold transition-all hover:shadow-lg duration-300 hover:scale-105 bg-[#00ADB5] mt-8 py-3 px-6"
              >
                Close
              </button>
            </div>
        </Transition>
      </div>
  </Transition>
</template>

<script setup>
defineEmits(["close"]);
defineProps({
  modalActive: {
    type: Boolean,
    default: false,
  },
});
</script>

<style  scoped>
.modal-outer-enter-active,
.modal-outer-leave-active {
  transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-outer-enter-from,
.modal-outer-leave-to {
  opacity: 0;
}

.modal-inner-enter-active {
  transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
}

.modal-inner-leave-active {
  transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-inner-enter-from {
  opacity: 0;
  transform: scale(0.8);
}

.modal-inner-leave-to {
  transform: scale(0.8);
}
</style>
