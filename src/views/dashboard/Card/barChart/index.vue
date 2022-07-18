<template>
  <div class="charts" ref="charts"></div>
</template>

<script>
import * as echarts from "echarts";
export default {
  name: "barChart",
  mounted() {
    const barChart = echarts.init(this.$refs.charts);
    barChart.setOption({
      xAxis: {
        show: false,
        type: "category",
      },
      yAxis: { show: false },
      series: [
        {
          type: "bar",
          data: [20, 35, 64, 92, 15, 15, 36, 98, 75, 45],
        },
      ],
      grid: {
        left: 0,
        right: 0,
        top: 0,
        bottom: 0,
      },
      tooltip: {},
    });
  },
};
</script>

<style>
.charts {
  width: 100%;
  height: 100%;
}
</style>