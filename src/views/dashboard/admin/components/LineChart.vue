<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from "echarts";
require("../components/chalk"); // echarts theme
import resize from "./mixins/resize";

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: "chart"
    },
    name:{
      type:String,
    },
    width: {
      type: String,
      default: "100%"
    },
    height: {
      type: String,
      default: "500px"
    },
    autoResize: {
      type: Boolean,
      default: true
    },
    chartData: {
      required: true
    }
  },
  data() {
    return {
      chart: null
    };
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val);
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart();
    });
  },
  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    this.chart.dispose();
    this.chart = null;
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, "chalk", { renderer: "svg" });
      // this.setOptions()
    },
    setOptions(data) {
      this.chart.clear();
      this.chart.setOption({
        legend: {
          data: data.legend
        },
        animation:false,
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          },
          padding: [5, 10]
        },
        grid: {
          left: 50,
          right: 20,
          bottom: 50,
          top: 50,
          containLabel: true
        },
          dataZoom: [
            {
              show: true,
              height: 30,
              xAxisIndex: [0],
              bottom: 0,
              start: 0,
              end: 100,
              handleIcon:
                "path://M306.1,413c0,2.2-1.8,4-4,4h-59.8c-2.2,0-4-1.8-4-4V200.8c0-2.2,1.8-4,4-4h59.8c2.2,0,4,1.8,4,4V413z",
              handleSize: "110%",
              handleStyle: {
                color: "#d3dee5"
              },
              textStyle: {
                color: "#fff"
              },
              borderColor: "#90979c"
            },
            {
              type: "inside",
              show: true,
              height: 15,
              start: 1,
              end: 35
            }
          ],
        // color: ["#2ec7c9", "#333", "#27727b", "#e01f54"],
        xAxis: {
          scale: true,
            name:'时间（ms）',
            nameLocation:'start',
            nameRotate:45,
            axisLabel: {
                interval: 0,
                rotate: -60
            }
        },
        yAxis: {
          scale: true,
          name:this.name,
        },
        series: data.data
      });
    }
  }
};
</script>
