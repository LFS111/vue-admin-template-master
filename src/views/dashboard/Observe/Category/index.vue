<template>
  <div>
    <el-card>
      <div slot="header">
        <div class="category-header">
          <span>销售额类别占比</span>
          <el-radio-group v-model="radio1">
            <el-radio-button label="全部渠道"></el-radio-button>
            <el-radio-button label="线上"></el-radio-button>
            <el-radio-button label="门店 "></el-radio-button>
          </el-radio-group>
        </div>
      </div>
      <div><div class="chartsss" ref="charts"></div></div>
    </el-card>
  </div>
</template>

<script>
import * as echarts from "echarts";
export default {
  name: "Category",
  data() {
    return {
      radio1: "全部渠道",
    };
  },
  mounted() {
    let mycharts = echarts.init(this.$refs.charts);
    mycharts.setOption({
      tooltip: {
        trigger: "item",
      },
      legend: {
        top: "5%",
        left: "center",
      },
      series: [
        {
          name: "爱好",
          type: "pie",
          radius: ["40%", "70%"],
          avoidLabelOverlap: false,
          itemStyle: {
            borderRadius: 10,
            borderColor: "#fff",
            borderWidth: 2,
          },
          label: {
            show: false,
            position: "center",
          },
          emphasis: {
            label: {
              show: true,
              fontSize: "40",
              fontWeight: "bold",
            },
          },
          labelLine: {
            show: false,
          },
          data: [
            { value: 1048, name: "学习" },
            { value: 735, name: "游戏" },
            { value: 580, name: "睡觉" },
            { value: 484, name: "吃饭" },
            { value: 300, name: "电影" },
          ],
        },
      ],
    });
  },
};
</script>

<style scoped>
.category-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
}
.chartsss {
  width: 100%;
  height: 270px;
}
</style>