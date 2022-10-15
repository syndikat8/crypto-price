<template>
  <div
      class="flex mt-4 items-end border-gray-600 border-b border-l h-64"
      ref="graph"
  >
    <div
        v-for="(bar, idx) in normalizedGraph"
        :key="idx"
        :style="{ height: `${bar}%` }"
        class="bg-yellow-300 border w-10"
    />
  </div>
</template>

<script>
export default {
  name: "GraphTicker",
  props: {
    graph: {
      type: Array,
      required: false,
      default: () => ([])
    }
  },
  computed: {
    normalizedGraph() {
      const maxValue = Math.max(...this.graph);
      const minValue = Math.min(...this.graph);

      if (maxValue === minValue) {
        return this.graph.map(() => 50);
      }

      return this.graph.map(
          price => 5 + ((price - minValue) * 95) / (maxValue - minValue)
      );
    }
  },
  methods: {
    calculateMaxGraphElements() {
      if (!this.$refs.graph) {
        return;
      }
      this.$emit('calculateMaxGraphElements', this.$refs.graph.clientWidth / 38)
    },
  }
}
</script>