<template>
    <div class="relative lg:max-w-xs w-full">
      <div class="flex absolute inset-y-0 left-0 items-center pl-3 pointer-events-none">
        <svg aria-hidden="true" class="w-5 h-5 text-gray-500 dark:text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
      </div>
      <input
          class="w-full block p-2 pl-10 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
          v-model="searchValue"
          placeholder="Найти тикер"
      >
    </div>
</template>

<script>
export default {
  name: "InputSearch",
  data: () => ({
    searchValue: ''
  }),
  watch: {
    searchValue () {
      this.$emit('update:filter', this.searchValue)
    }
  }
}
</script>

<style scoped>

</style>