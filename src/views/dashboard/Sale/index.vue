<template>
  <el-card class="box-card" style="margin: 10px 0">
    <div slot="header" class="clearfix">
      <!-- type="card" @tab-click="handleClick" -->
      <el-tabs class="tab" v-model="activeName">
        <el-tab-pane label="销售额" name="销售额"></el-tab-pane>
        <el-tab-pane label="访问量" name="访问量"> </el-tab-pane>
      </el-tabs>
      <div class="right">
        <span @click="setDay">今日</span>
        <span @click="setWeek">本周</span>
        <span @click="setMonth">本月</span>
        <span @click="setYear">本年</span>
        <!-- v-model="value1" -->
        <el-date-picker
          v-model="date"
          class="dateaa"
          type="datetimerange"
          range-separator="-"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          size="mini"
          value-format="yyyy-MM-dd"
        >
        </el-date-picker>
      </div>
    </div>
    <div>
      <el-row :gutter="10">
        <el-col :span="16"><div class="charts" ref="charts"></div></el-col>
        <el-col :span="8"
          ><div class="rights">
            <h3>门店{{ activeName }}排名</h3>
            <ul>
              <li>
                <span class="circle">1</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
              <li>
                <span class="circle">2</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
              <li>
                <span class="circle">3</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
              <li>
                <span class="circle">4</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
              <li>
                <span class="circle">5</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
              <li>
                <span class="circle">6</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
              <li>
                <span class="circle">7</span> <span>肯德基</span>
                <span class="rr">123456</span>
              </li>
            </ul>
          </div></el-col
        >
      </el-row>
    </div>
  </el-card>
</template>

<script>
import * as echarts from "echarts";
import dayjs from "dayjs";

export default {
  name: "Sale",
  data() {
    return {
      activeName: "销售额",
      mycharts: null,
      date: [],
    };
  },
  watch: {
    activeName() {
      this.mycharts.setOption({
        title: {
          text: this.activeName + "趋势",
        },
      });
    },
  },
  mounted() {
    this.mycharts = echarts.init(this.$refs.charts);
    this.mycharts.setOption({
      title: {
        text: this.activeName + "趋势",
      },
      tooltip: {
        trigger: "axis",
        axisPointer: {
          type: "shadow",
        },
      },
      grid: {
        left: "3%",
        right: "4%",
        bottom: "3%",
        containLabel: true,
      },
      xAxis: [
        {
          type: "category",
          data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
          axisTick: {
            alignWithLabel: true,
          },
        },
      ],
      yAxis: [
        {
          type: "value",
        },
      ],
      series: [
        {
          name: "Direct",
          type: "bar",
          barWidth: "60%",
          data: [10, 52, 200, 334, 390, 330, 220],
        },
      ],
    });
  },
  methods: {
    setDay() {
      const day = dayjs().format("YYYY-MM-DD");
      this.date = [day, day];
    },
    setWeek() {
      const start = dayjs().day(1).format("YYYY-MM-DD");
      const end = dayjs().day(7).format("YYYY-MM-DD");
      this.date = [start, end];
    },
    setMonth() {
      const start = dayjs().startOf("month").format("YYYY-MM-DD");
      const end = dayjs().endOf("month").format("YYYY-MM-DD");
      this.date = [start, end];
    },
    setYear() {
      const start = dayjs().startOf("year").format("YYYY-MM-DD");
      const end = dayjs().endOf("year").format("YYYY-MM-DD");
      this.date = [start, end];
    },
  },
};
</script>

<style scoped>
.clearfix {
  position: relative;
  display: flex;
  justify-content: space-between;
}
.tab {
  width: 100%;
}
.right {
  position: absolute;
  right: 0;
}
.dateaa {
  width: 250px;
  margin: 0 20px;
}
.right span {
  margin: 0 10px;
}
.charts {
  width: 100%;
  height: 300px;
}
ul {
  list-style: none;
  width: 100%;
  height: 300px;
}
ul li {
  height: 8%;
  margin: 10px 0;
}
.circle {
  float: left;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: black;
  color: white;
  text-align: center;
  margin-right: 20px;
}
.rr {
  float: right;
}
</style>