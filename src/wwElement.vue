<template>
  <div class="chart-container" style="position: relative">
    <canvas class="chartjs-radar"></canvas>
  </div>
</template>

<script>
import { Chart, registerables } from "chart.js";
Chart.register(...registerables);

export default {
  props: {
    content: { type: Object, required: true },
  },
  data() {
    this.chartInstance = null;
    return this.chartInstance;
  },
  computed: {
    options() {
      const guidedOptions = {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: this.content.isLegend,
            position: this.content.legendPosition,
            align: this.content.legendAlignement,
            labels: {
              usePointStyle: true,
              color: this.content.legendColor,
              font: { size: parseInt(this.content.legendSize) },
            },
          },
        },
      };

      const advancedOptions =
        typeof this.content.options === "object"
          ? this.content.options
          : guidedOptions;
      return this.content.dataType === "advanced"
        ? advancedOptions
        : guidedOptions;
    },
    config() {
      let labels = [];
      let datasets = [];

      if (this.content.dataType === "guided") {
        let data =
          (!this.content.data || Array.isArray(this.content.data)
            ? this.content.data
            : this.content.data.data) || [];

        const yAxis = this.content.yAxis;
        let dataXField = this.content.dataXField;
        let dataXFieldProperty = this.content.dataXFieldProperty;
        let dataYField =
          yAxis === "item-count"
            ? this.content.dataXField
            : this.content.dataYField;
        let dataYFieldProperty = this.content.dataYFieldProperty;
        let aggregate =
          yAxis === "item-count" ? "item-count" : this.content.aggregate;
        const colors = this.content.colors;

        if (typeof data[0] !== "object") {
          data = data.map((value) => ({ value }));
          dataXField = "value";
          dataYFieldProperty = undefined;
          dataYField = "value";
          dataXFieldProperty = undefined;
          aggregate = "item-count";
        }

        datasets = [
          {
            label: `${dataXField}`.split("['").pop().replace("']", ""),
            backgroundColor: colors,
            data: [],
          },
        ];
        if (!data.length || !Array.isArray(_.get(data[0], dataXField))) {
          datasets[0].data = data.map((item) => ({
            x: _.get(item, dataXField),
            y: this.aggregate(
              aggregate,
              data
                .filter(
                  (elem) => _.get(elem, dataXField) === _.get(item, dataXField)
                )
                .map((elem) => _.get(elem, dataYField))
            ),
          }));
        } else {
          const arrayValues = [
            ...new Set(
              data
                .map((item) => {
                  const result = _.get(item, dataXField, []);
                  return Array.isArray(result)
                    ? result.map((elem) =>
                        _.get(elem, dataXFieldProperty, elem)
                      )
                    : result;
                })
                .flat()
            ),
          ];
          datasets[0].data = arrayValues.map((arrayValue) => ({
            x: arrayValue,
            y: this.aggregate(
              aggregate,
              data
                .filter((item) => {
                  let result = _.get(item, dataXField, []);
                  result = Array.isArray(result)
                    ? result.map((elem) =>
                        _.get(elem, dataXFieldProperty, elem)
                      )
                    : result;
                  return result.includes(arrayValue);
                })
                .map((item) => {
                  const result = _.get(item, dataYField, []);
                  return Array.isArray(result)
                    ? result.map((elem) =>
                        _.get(elem, dataYFieldProperty, elem)
                      )
                    : result;
                })
            ),
          }));
        }

        // Remove duplicate X values
        for (const dataset of datasets) {
          dataset.data = dataset.data.filter(
            (item, index) =>
              dataset.data.findIndex((elem) => item.x === elem.x) === index
          );
        }
        // Order by
        if (this.content.dataOrderBy !== "default") {
          for (const item of datasets) {
            item.data.sort((a, b) => {
              const field = this.content.dataOrderBy;
              return (
                (typeof a[field] === "string"
                  ? a[field].localeCompare(b[field])
                  : a[field] - b[field]) *
                (this.content.dataDirection === "DESC" ? -1 : 1)
              );
            });
            item.data = item.data.map((item) => ({
              x: `${item.x}`,
              y: item.y,
            }));
          }
        }
        // Empty values
        if (this.content.dataXEmpty === false) {
          for (const dataset of datasets) {
            dataset.data = dataset.data.filter((item) => item.x && item.y);
          }
        }
        labels = [
          ...new Set(
            datasets.map((dataset) => dataset.data.map((elem) => elem.x)).flat()
          ),
        ];
        // Format data
        for (const dataset of datasets) {
          dataset.data = dataset.data.map((item) => item.y);
        }
      } else {
        labels = this.content.labels;
        datasets = this.content.datasets || [];
      }

      return {
        type: "radar",
        data: {
          labels,
          datasets,
        },
        options: this.options,
      };
    },
  },
  watch: {
    "config.data.datasets"() {
      this.chartInstance.data.datasets = this.config.data.datasets;
      if (this.chartInstance) this.chartInstance.destroy();
      this.initChart();
      this.chartInstance.update();
    },
    options: {
      deep: true,
      handler() {
        console.log(this.options);
        this.chartInstance.data.datasets = this.config.data.datasets;
        if (this.chartInstance) this.chartInstance.destroy();
        this.initChart();
        this.chartInstance.update();
      },
    },
    "config.data.labels"() {
      this.chartInstance.data.labels = this.config.data.labels;
      this.chartInstance.update();
    },
    "content.isLegend"() {
      this.chartInstance.options.plugins.legend.display = this.content.isLegend;
      this.chartInstance.update();
    },
    "content.legendPosition"() {
      this.chartInstance.options.plugins.legend.position =
        this.content.legendPosition;
      this.chartInstance.update();
    },
    "content.legendAlignement"() {
      this.chartInstance.options.plugins.legend.align =
        this.content.legendAlignement;
      this.chartInstance.update();
    },
    "content.legendColor"() {
      this.chartInstance.options.plugins.legend.labels.color =
        this.content.legendColor;
      this.chartInstance.update();
    },
    "content.legendSize"() {
      this.chartInstance.options.plugins.legend.labels.font.size = parseInt(
        this.content.legendSize
      );
      this.chartInstance.update();
    },
    "content.dataYField"() {
      // eslint-disable-next-line vue/require-explicit-emits
      this.$emit("update:content:effect", { dataYFieldProperty: null });
    },
    "content.dataYFieldProperty"() {
      const data =
        (!this.content.data || Array.isArray(this.content.data)
          ? this.content.data
          : this.content.data.data) || [];
      let field = _.get(data[0], this.content.dataYField);
      const isArray = Array.isArray(field);
      if (Array.isArray(field) && field.length)
        field = _.get(field[0], this.content.dataYFieldProperty, field[0]);
      const isNumber = Number.isFinite(
        data[0] && this.content.dataYField && field
      );
      if (isNumber) {
        // eslint-disable-next-line vue/require-explicit-emits
        this.$emit("update:content:effect", {
          aggregate: isArray ? "sum" : "value",
        });
      }
    },
    "content.dataXField"() {
      // eslint-disable-next-line vue/require-explicit-emits
      this.$emit("update:content:effect", { dataXFieldProperty: null });
    },
    "content.groupBy"() {
      // eslint-disable-next-line vue/require-explicit-emits
      this.$emit("update:content:effect", { groupByProperty: null });
    },
  },
  mounted() {
    this.initChart();
  },
  beforeUnmount() {
    this.chartInstance.destroy();
  },
  methods: {
    initChart() {
      const element = this.$el.querySelector(".chartjs-radar");
      this.chartInstance = new Chart(element, this.config);
    },
    aggregate(operator, data) {
      if (!data) return undefined;
      switch (operator) {
        case "item-count":
          return [data].flat().length;
        case "distinct":
          return [...new Set([data].flat())].length;
        case "value":
          return [data].flat()[0];
        case "sum":
          return this.sum([data].flat());
        case "average":
          return this.average([data].flat());
        case "median":
          return this.median([data].flat());
        case "min":
          return Math.min(...[data].flat());
        case "max":
          return Math.max(...[data].flat());
      }
    },
    median(arr) {
      const mid = Math.floor(arr.length / 2),
        nums = [...arr].sort((a, b) => a - b);
      return arr.length % 2 !== 0 ? nums[mid] : (nums[mid - 1] + nums[mid]) / 2;
    },
    sum(arr) {
      return arr.reduce((a, b) => a + b, 0);
    },
    average(arr) {
      return arr.reduce((a, b) => a + b, 0) / arr.length;
    },
  },
};
</script>

<style lang="scss" scoped>
.chart-container {
  #chartjs-radar {
    width: 100% !important;
    height: 100% !important;
  }
}
</style>
