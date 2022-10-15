<template>
  <button
    type="button"
    :disabled="disabled"
    class="inline-flex items-center py-4 px-7 rounded-md border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-purple-700 hover:bg-purple-600 transition-colors duration-300"
    :class="{
      'opacity-50': disabled
    }"
  >
    Добавить
  </button>
</template>

<script>
export default {
  props: {
    disabled: {
      type: Boolean,
      required: false,
      default: false
    }
  }
};
</script>
