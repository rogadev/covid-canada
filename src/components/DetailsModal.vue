<script setup>
const props = defineProps({
  province: {
    type: String,
    required: true,
  },
  image: {
    type: String,
    required: true,
  },
  details: {
    type: Object,
    required: true,
  },
});
</script>

<template>
  <div
    class="modal-container"
    aria-labelledby="modal-title"
    role="dialog"
    aria-modal="true"
  >
    <div class="modal-wrapper">
      <div
        class="background-overlay"
        aria-hidden="true"
        @click="$emit('closeModal')"
      ></div>

      <!-- This element is to trick the browser into centering the modal contents. -->
      <span
        class="hidden sm:inline-block sm:align-middle sm:h-screen"
        aria-hidden="true"
        >&#8203;</span
      >

      <div class="modal-panel">
        <div id="content-wrapper">
          <div class="image-wrapper">
            <img class="province-image" :src="image" :alt="province" />
          </div>
          <div class="mt-3 text-center sm:mt-5">
            <h3
              class="text-lg leading-6 font-medium text-gray-900"
              id="modal-title"
            >
              {{ province }}
            </h3>
            <div class="mt-2">
              <p>
                {{ Intl.NumberFormat().format(details.cases) }}
                new cases as of
                {{ details.date }}
              </p>
              <p>{{ n }}</p>
            </div>
          </div>
        </div>
        <div class="button-wrapper">
          <button type="button" @click="$emit('closeModal')">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
button {
  @apply inline-flex justify-center w-full rounded-md border border-transparent shadow-sm px-4 py-2 bg-indigo-600 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:text-sm;
}

.modal-container {
  @apply fixed z-10 inset-0 overflow-y-auto;
}

.modal-wrapper {
  @apply flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0;
}

/* Background overlay, show/hide based on modal state.
    Entering: "ease-out duration-300"
      From: "opacity-0"
      To: "opacity-100"
    Leaving: "ease-in duration-200"
      From: "opacity-100"
      To: "opacity-0" */
.background-overlay {
  @apply fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity;
}

/* Modal panel, show/hide based on modal state.
    Entering: "ease-out duration-300"
      From: "opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
      To: "opacity-100 translate-y-0 sm:scale-100"
    Leaving: "ease-in duration-200"
      From: "opacity-100 translate-y-0 sm:scale-100"
      To: "opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95" */
.modal-panel {
  @apply relative inline-block align-bottom bg-white rounded-lg px-4 pt-5 pb-4 text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-sm sm:w-full sm:p-6;
}

.image-wrapper {
  @apply mx-auto flex items-center justify-center h-12 w-12;
}

.button-wrapper {
  @apply mt-5 sm:mt-6;
}

.province-image {
  @apply h-10;
}
</style>