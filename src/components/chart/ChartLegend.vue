<template>
  <StandardLegend
    v-if="showStandardLegend"
    :key="range + '-' + legendVersion"
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
    range: {
      type: String,
      default: "day",
    },
  },
  data() {
    return {
      standard: false,
    };
  },
  computed: {
    legendItems() {
      //console.log("Chart or datasets not ready standard", this.chart);
      if (!this.chart || !this.chart.data || !Array.isArray(this.chart.data.datasets)) {
        return [];
      }

      const hidden = this.$store.state.chartLegend.hiddenDatasets;
      return this.chart.data.datasets.map((dataset, index) => ({
        label: dataset.label,
        index,
        category: dataset.category || "component",
        hidden: hidden.includes(dataset.label),
        borderColor: dataset.borderColor,
        borderDash: dataset.borderDash,
      }));
    },
    categorizedLegendItems() {
      if (!this.chart) {
        return { chargepoint: [], vehicle: [], component: [] };
      }
      const hidden = this.$store.state.chartLegend.hiddenDatasets;
      console.log("Categorizing legend items, hidden:", hidden);
      const categories = { chargepoint: [], vehicle: [], component: [] };
      const datasets = this.chart?.data?.datasets || [];
      datasets.forEach((dataset, index) => {
        const cat = dataset.category || "component";
        if (!categories[cat]) categories[cat] = [];
        categories[cat].push({
          label: dataset.label,
          index,
          hidden: hidden.includes(dataset.label),
          borderColor: dataset.borderColor,
          borderDash: dataset.borderDash,
        });
      });
      return categories;
    },
    showStandardLegend() {
      //Standard-Legende for less than 15 Items or if range is month or year
      return this.legendItems.length < 15 || this.range === "month" || this.range === "year";
    },
  },
  watch: {
    chart(newChart) {
      if (newChart) {
        this.applyHiddenDatasetsToChart();
      }
    },
  },
  mounted() {
    // Initial Hidden-Status aus Store anwenden
    this.applyHiddenDatasetsToChart();
  },
  methods: {
    toggleDataset(indexOrLabel) {
      if (!this.chart) return;
      // Hole das Label des Datasets
      const dataset =
        typeof indexOrLabel === "number"
          ? this.chart.data.datasets[indexOrLabel]
          : this.chart.data.datasets.find((ds) => ds.label === indexOrLabel);

      if (!dataset) return;

      // Toggle im Store
      this.$store.commit("chartLegend/toggleDataset", dataset.label);

      // Hidden-Status aus Store anwenden
      this.applyHiddenDatasetsToChart();

      //this.legendVersion++; // erzwingt Neuberechnung
    },
    applyHiddenDatasetsToChart() {
      if (!this.chart || !this.chart.data) return;
      const hidden = this.$store.state.chartLegend.hiddenDatasets;
      this.chart.data.datasets.forEach((ds) => {
        ds.hidden = hidden.includes(ds.label);
      });
      this.chart.update();
    },
  },
};
</script>
