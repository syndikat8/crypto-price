<template>
  <div class="mt-5 pt-3 border-t border-gray-200 border-solid flex justify-between items-center">
    <div class="sm:flex hidden">
      Показано {{ paginatedTickers.length }} результатов из {{ tickers.length }}
    </div>
    <div class="w-full flex justify-between lg:w-max">
      <button
          v-if="page > 1"
          class="mr-4 inline-flex items-center py-3 px-6 rounded-md border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-grey-700 bg-white"
          @click="prevPage"
      >
        Назад
      </button>
      <button
          v-if="hasNextPage"
          class="inline-flex items-center py-3 px-6 rounded-md border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-grey-700 bg-white"
          @click="nextPage"
      >
        Вперед
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "CustomPagination",
  props: {
    page: {
      type: Number,
      default: 0
    },
    hasNextPage: {
      type: Boolean,
      default: false
    },
    paginatedTickers: {
      type: Array,
      default: () => ([])
    },
    tickers: {
      type: Array,
      default: () => ([])
    }
  },
  computed: {
    updatePage: {
      get () {
        return this.page
      },
      set (data) {
        this.$emit('update:page', data)
      }
    }
  },
  methods: {
    prevPage () {
      this.updatePage -= 1
    },
    nextPage () {
      this.updatePage += 1
    }
  }

}
</script>