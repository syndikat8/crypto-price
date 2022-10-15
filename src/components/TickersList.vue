<template>
  <dl class="mt-5 grid grid-cols-1 gap-5 sm:grid-cols-3">
    <div v-for="t in paginatedTickers"  :key="t.name">
      <div
          @click="onSelect(t)"
          :class="{'border-4': selectedTicker === t}"
          class="bg-white overflow-hidden shadow rounded-lg border-black border-solid cursor-pointer"
      >
        <div class="px-5 py-4">
          <div class="flex justify-between">
            <div class="text-sm font-medium text-gray-500 truncate">
              {{ t.name }}
            </div>
            <div
                class="w-5"
                @click.stop="handleDelete(t)"
            >
              <RemoveIcon/>
            </div>
          </div>
          <dd class="mt-1 text-3xl text-gray-900">
            {{ formatPrice(t.price) }}$
          </dd>
        </div>
      </div>
      <GraphTicker
          v-if="selectedTicker.name === t.name"
          class="lg:hidden"
          :graph="graph"
          @calculateMaxGraphElements="calculateMaxGraphElements"
      />
    </div>
  </dl>
</template>

<script>
import { RemoveIcon } from  "./../constants/icons"
import GraphTicker from "./../components/GraphTicker.vue";

export default {
  name: "TickersList",
  components: {
    RemoveIcon,
    GraphTicker
  },
  props: {
    paginatedTickers: {
      type: Array,
      default: () => ([])
    },
    selectedTicker: {
      type: Object,
      default: () => ({})
    },
    graph: {
      type: Array,
      required: false,
      default: () => ([])
    }
  },
  methods: {
    onSelect (ticker) {
      this.$emit('onSelect', ticker)
    },
    handleDelete (ticker) {
      this.$emit('handleDelete', ticker)
    },
    formatPrice(price) {
      if (price === "-") {
        return price;
      }
      return price > 1 ? price.toFixed(2) : price.toPrecision(2);
    },
    calculateMaxGraphElements (width) {
      this.$emit('calculateMaxGraphElements', width)
    }
  }
}
</script>

<style scoped>

</style>