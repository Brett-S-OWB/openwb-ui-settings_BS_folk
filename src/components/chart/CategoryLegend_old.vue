<template>
  <div class="dropdown">
    <button
      id="dropdownMenuButton1"
      class="btn btn-secondary dropdown-toggle"
      type="button"
      data-toggle="dropdown"
      aria-expanded="false"
    >
      Ladepunkte
    </button>
    <ul
      class="dropdown-menu"
      aria-labelledby="chargepointDropdown"
    >
      <li
        v-for="item in categorizedLegendItems.component"
        :key="item.label"
        @click="toggleDataset(item.index)"
      >
        <span :class="{ 'text-decoration-line-through': chart.data.datasets[item.index].hidden }"
          >{{ item.label }}
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
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
    };
  },
  computed: {
    categorizedLegendItems() {
      this.legendVersion;
      const categories = {
        chargepoint: [],
        vehicle: [],
        battery: [],
        component: [],
      };
      //console.log("Categorizing datasets...", categories);
      if (!this.chart || !this.chart.data || !Array.isArray(this.chart.data.datasets)) {
        return categories;
      }
      this.chart.data.datasets.forEach((dataset, index) => {
        const category = dataset.category || "component";
        categories[category].push({
          label: dataset.label,
          index,
        });
      });
      console.log("Categorizing datasets...", categories);
      return categories;
    },
  },
  methods: {
    toggleDataset(index) {
      const chart = this.chart;
      if (!chart) return;
      const dataset = chart.data.datasets[index];
      dataset.hidden = !dataset.hidden;
      chart.update();
      const datasetnow = chart.data.datasets;
      console.log("Toggling dataset...", index, dataset, datasetnow);
      this.legendVersion++;
    },
  },
};
</script>

<style>
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
