<template>
  <div class="dropdown">
    <button
      class="btn btn-secondary dropdown-toggle"
      type="button"
      data-bs-toggle="dropdown"
      aria-expanded="false"
      id="dropdownMenuButton1"
    >
      Ladepunkte
    </button>
    <ul
      class="dropdown-menu"
      aria-labelledby="dropdownMenuButton1"
    >
      <li
        v-for="item in categorizedLegendItems.chargepoint"
        :key="item.label"
        @click="toggleDataset(item.index)"
      >
        <span :class="{ 'text-decoration-line-through': item.hidden }">{{ item.label }}</span>
      </li>
    </ul>
  </div>
  <div class="dropdown">
    <!-- Dropdown Button -->
    <button
      class="btn btn-primary dropdown-toggle"
      type="button"
      id="dropdownMenuButton"
      data-bs-toggle="dropdown"
      aria-expanded="false"
    >
      {{ selected }}
    </button>

    <!-- Dropdown Menu -->
    <ul
      class="dropdown-menu"
      aria-labelledby="dropdownMenuButton"
    >
      <li
        v-for="(option, index) in options"
        :key="index"
      >
        <a
          class="dropdown-item"
          href="#"
          @click="selectOption(option)"
        >
          {{ option }}
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
import * as bootstrap from "bootstrap";
console.log("Bootstrap loaded:", bootstrap);
export default {
  name: "CategoryLegend",
  props: {
    chart: {
      type: Object,
      default: () => null,
    },
  },
  data() {
    return {
      legendVersion: 0, // used to force reactive update of legendItems
      selected: "Choose an option",
      options: ["Option 1", "Option 2", "Option 3"],
    };
  },
  computed: {
    categorizedLegendItems() {
      const categories = {
        chargepoint: [],
        vehicle: [],
        battery: [],
        component: [],
      };
      console.log("Categorizing datasets...", categories);
      if (!this.chart || !this.chart.data || !Array.isArray(this.chart.data.datasets)) {
        return categories;
      }
      this.chart.data.datasets.forEach((dataset, index) => {
        const cat = dataset.category || "component";
        categories[cat].push({
          ...dataset,
          index,
          hidden: dataset.hidden || false,
        });
      });
      return categories;
    },
  },
  mounted() {
    const dropdownElement = document.getElementById("dropdownMenuButton1");
    if (dropdownElement) {
      this.dropdown = new bootstrap.Dropdown(dropdownElement, {
        autoClose: true, // this is the default
      });
    }
  },
  methods: {
    toggleDataset(index) {
      const chart = this.chart;
      if (!chart) return;
      const dataset = chart.data.datasets[index];
      dataset.hidden = !dataset.hidden;
      chart.update();
      this.legendVersion++;
    },
    selectOption(option) {
      this.selected = option;
    },
  },
};
</script>

<style scoped>
.custom-legend {
  cursor: pointer;
  font-size: 0.875rem;
  user-select: none;
}

.legend-item {
  padding: 2px 6px;
  border-radius: 4px;
  transition: opacity 0.2s;
}

.legend-item:hover {
  background-color: rgba(0, 0, 0, 0.05);
}

.legend-item-hidden {
  opacity: 0.5;
}

.legend-label {
  white-space: nowrap;
}

.text-decoration-line-through {
  text-decoration: line-through;
}
</style>
