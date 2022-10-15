<template>
  <div class="container mx-auto flex flex-col items-center bg-gray-100 p-4">
    <div class="container">
      <div class="w-full my-4"></div>
      <add-ticker @add-ticker="add" />
      <template v-if="tickers.length">
        <InputSearch
          v-model:filter="filter"
        />
        <TickersList
            :paginatedTickers="paginatedTickers"
            :selectedTicker="selectedTicker"
            :graph="graph"
            @onSelect="select"
            @handleDelete="handleDelete"
            @calculateMaxGraphElements="calculateMaxGraphElements"
        />
      </template>
      <GraphTicker
          v-if="selectedTicker.name"
          class="hidden lg:flex"
          :graph="graph"
          @calculateMaxGraphElements="calculateMaxGraphElements"
      />
      <CustomPagination
          v-if="tickers.length"
          v-model:page="page"
          :hasNextPage="hasNextPage"
          :paginatedTickers="paginatedTickers"
          :tickers="tickers"
      />
    </div>
  </div>
</template>

<script>

import { subscribeToTicker, unsubscribeFromTicker } from "./api";
import AddTicker from "./components/AddTicker.vue";
import GraphTicker from "./components/GraphTicker.vue";
import InputSearch from "./components/InputSearch.vue";
import TickersList from "./components/TickersList.vue";
import CustomPagination from "./components/CustomPagination.vue";

export default {
  name: "App",
  components: {
    AddTicker,
    GraphTicker,
    InputSearch,
    TickersList,
    CustomPagination,
  },
  data() {
    return {
      filter: "",
      tickers: [],
      selectedTicker: {},
      graph: [],
      maxGraphElements: 1,
      page: 1
    };
  },
  created() {
    const windowData = Object.fromEntries(
        new URL(window.location).searchParams.entries()
    );
    const VALID_KEYS = ["filter", "page"];
    VALID_KEYS.forEach(key => {
      if (windowData[key]) {
        this[key] = windowData[key];
      }
    });
    if (windowData.filter) {
      this.filter = windowData.filter;
    }
    if (windowData.page) {
      this.page = windowData.page;
    }
    const tickersData = localStorage.getItem("cryptonomicon-list");
    if (tickersData) {
      this.tickers = JSON.parse(tickersData);
      this.tickers.forEach(ticker => {
        subscribeToTicker(ticker.name, newPrice =>
            this.updateTicker(ticker.name, newPrice)
        );
      });
    }
    setInterval(this.updateTicker, 5000);
  },
  computed: {
    tooManyTickersAdded() {
      return this.tickers.length > 4;
    },
    startIndex() {
      return (this.page - 1) * 6;
    },
    endIndex() {
      return this.page * 6;
    },
    filteredTickers() {
      return this.tickers.filter(ticker => ticker.name.includes(this.filter));
    },
    paginatedTickers() {
      return this.filteredTickers.slice(this.startIndex, this.endIndex);
    },
    hasNextPage() {
      return this.filteredTickers.length > this.endIndex;
    },
    normalizedGraph() {
      const maxValue = Math.max(...this.graph);
      const minValue = Math.min(...this.graph);

      if (maxValue === minValue) {
        return this.graph.map(() => 50);
      }

      return this.graph.map(
          price => 5 + ((price - minValue) * 95) / (maxValue - minValue)
      );
    },
    pageStateOptions() {
      return {
        filter: this.filter,
        page: this.page
      };
    }
  },
  methods: {
    calculateMaxGraphElements(width) {
      this.maxGraphElements = width;
    },
    updateTicker(tickerName, price) {
      this.tickers
          .filter(t => t.name === tickerName)
          .forEach(t => {
            if (t === this.selectedTicker) {
              this.graph.push(price);
              while (this.graph.length > this.maxGraphElements) {
                this.graph.shift();
              }
            }
            t.price = price;
          });
    },
    add(name) {
      const currentTicker = {
        name: name,
        price: "-",
      };
      this.tickers = [...this.tickers, currentTicker];
      this.filter = "";
      subscribeToTicker(name, newPrice => this.updateTicker(name, newPrice));
    },
    select(ticker) {
      this.selectedTicker = ticker;
    },
    handleDelete(tickerToRemove) {
      this.tickers = this.tickers.filter(t => t !== tickerToRemove);
      if (this.selectedTicker === tickerToRemove) {
        this.selectedTicker = {};
      }
      unsubscribeFromTicker(tickerToRemove.name);
    }
  },
  watch: {
    selectedTicker() {
      this.graph = [];

      this.$nextTick().then(this.calculateMaxGraphElements);
    },
    tickers() {
      localStorage.setItem("cryptonomicon-list", JSON.stringify(this.tickers));
    },
    paginatedTickers() {
      if (this.paginatedTickers.length === 0 && this.page > 1) {
        this.page -= 1;
      }
    },
    filter() {
      this.page = 1;
    },
    pageStateOptions(value) {
      window.history.pushState(
          null,
          document.title,
          `${window.location.pathname}?filter=${value.filter}&page=${value.page}`
      );
    }
  }
};
</script>
