<!-- eslint-disable no-debugger -->
<template>
  <StandardLegend
    v-if="showStandardLegend"
    :items="legendItems"
    @toggle="toggleDataset"
  />
  <LegendCategoriesGroup
    v-else
    :categorized-items="categorizedLegendItems"
    @toggle="toggleDataset"
  />
</template>

<script>
import StandardLegend from "./StandardLegend.vue";
import LegendCategoriesGroup from "./LegendCategoriesGroup.vue";

export default {
  name: "ChartLegend",
  components: { LegendCategoriesGroup, StandardLegend },
  props: {
    chart: {
      type: Object,
      default: () => null,
    },
  },
  data() {
    return {
      legendVersion: 0,
      standard: false,
    };
  },
  computed: {
    legendItems() {
      this.legendVersion; // Reaktivität erzwingen!
      console.log("Chart or datasets not ready standard", this.chart);
      if (!this.chart || !this.chart.data || !Array.isArray(this.chart.data.datasets)) {
        return [];
      }

      const LegendItems = this.chart.data.datasets.map((dataset, index) => ({
        label: dataset.label,
        index,
        category: dataset.category || "component",
        hidden: !!dataset.hidden,
        borderColor: dataset.borderColor,
        borderDash: dataset.borderDash,
      }));
      // // eslint-disable-next-line no-debugger
      // debugger;
      console.log("Legend items:", LegendItems);
      return LegendItems;
    },
    categorizedLegendItems() {
      this.legendVersion;
      if (!this.chart) {
        console.log("Chart or datasets not ready", this.chart);
        return { chargepoint: [], vehicle: [], component: [] };
      }
      const categories = { chargepoint: [], vehicle: [], component: [] };
      const datasets = this.chart?.data?.datasets || [];
      datasets.forEach((dataset, index) => {
        const cat = dataset.category || "component";
        //console.log("Dataset category:", cat, dataset);
        if (!categories[cat]) categories[cat] = [];
        categories[cat].push({
          label: dataset.label,
          index,
          hidden: !!dataset.hidden,
          borderColor: dataset.borderColor,
          borderDash: dataset.borderDash,
          // weitere Felder nach Bedarf
        });
      });
      console.log("Categorized legend items:", categories);
      return categories;
    },
    showStandardLegend() {
      // Beispiel: Standard-Legende bei weniger als 20 Items
      return this.legendItems.length < 10;
    },
  },
  // watch: {
  //   chart: {
  //     handler(newChart) {
  //       if (newChart && newChart.data && Array.isArray(newChart.data.datasets)) {
  //         this.$nextTick(() => {
  //           this.legendVersion++;
  //         });
  //       }
  //     },
  //     immediate: true,
  //     deep: true,
  //   },
  //   "chart.data.datasets": {
  //     handler() {
  //       this.$nextTick(() => {
  //         this.legendVersion++;
  //       });
  //     },
  //     deep: true,
  //   },
  // },
  methods: {
    toggleDataset(index) {
      if (!this.chart) return;
      const dataset = this.chart.data.datasets[index];
      dataset.hidden = !dataset.hidden;
      this.chart.update();
      this.legendVersion++; // erzwingt Neuberechnung
    },
  },
};
</script>
