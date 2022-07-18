<template>
  <div class="charts" ref="charts"></div>
</template>

<script>
import * as echarts from "echarts";
export default {
  name: "progressChart",
  mounted() {
    const barChart = echarts.init(this.$refs.charts);
    barChart.setOption({
      xAxis: {
        show: false,
        min: 0,
        max: 100,
      },
      yAxis: { show: false, type: "category" },
      series: [
        {
          type: "bar",
          data: [78],
          barWidth: 10,
          color: "yellowgreen",
          showBackground: true,
          //设置背景颜色
          backgroundStyle: {
            color: "#eee",
          },
          //设置文本标签
          label: {
            show: true,
            formatter: "|",
            position: "right",
          },
        },
      ],
      grid: {
        left: 0,
        right: 0,
        top: 0,
        bottom: 0,
      },
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