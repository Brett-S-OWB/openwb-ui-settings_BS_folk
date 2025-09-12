<template>
  <div class="custom-legend d-flex flex-wrap">
    <div
      v-for="(item, index) in legendItems"
      :key="item.label"
      class="legend-item d-flex align-items-center m-1"
      :class="{ 'legend-item-hidden': item.hidden }"
      role="button"
      @click="toggleDataset(index)"
    >
      <svg
        width="20"
        height="6"
        class="mr-1"
      >
        <line
          x1="0"
          y1="3"
          x2="20"
          y2="3"
          :stroke="item.borderColor"
          stroke-width="3"
          :stroke-dasharray="item.borderDash?.join(',')"
        />
      </svg>
      <span
        class="legend-label"
        :class="{ 'text-decoration-line-through': item.hidden }"
      >
        {{ item.label }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "StandardLegend",
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
    legendItems() {
      this.legendVersion; // um Reaktivität zu erzwingen
      if (!this.chart || !this.chart.data || !Array.isArray(this.chart.data.datasets)) {
        return [];
      }
      // Immer direkt aus chart.data.datasets lesen
      const data = this.chart.data.datasets;
      console.log("Initial datasets:", data);
      return this.chart.data.datasets.map((dataset) => {
        return {
          label: dataset.label,
          borderColor: dataset.borderColor,
          borderDash: dataset.borderDash,
          hidden: dataset.hidden || false,
        };
      });
    },
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
