<template>
  <el-card>
    <div class="search-header">
      <span>线上热门搜索</span>
      <el-dropdown>
        <span>
          <i class="el-icon-more"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>黄金糕</el-dropdown-item>
          <el-dropdown-item>狮子头</el-dropdown-item>
          <el-dropdown-item>螺蛳粉</el-dropdown-item>
          <el-dropdown-item>双皮奶</el-dropdown-item>
          <el-dropdown-item>蚵仔煎</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
    <div>
      <el-row :gutter="10">
        <el-col :span="12"><lineCharts></lineCharts></el-col>
        <el-col :span="12"><lineCharts></lineCharts></el-col>
      </el-row>
    </div>
    <el-table
      border
      :data="tableData"
      style="width: 100%; margin: 20px 0"
      :default-sort="{ prop: 'date', order: 'descending' }"
    >
      <el-table-column label="排名" width="80" type="index"> </el-table-column>
      <el-table-column label="搜索关键字" width="180"> </el-table-column>
      <el-table-column label="用户数" sortable> </el-table-column>
      <el-table-column label="周涨幅 " sortable> </el-table-column>
    </el-table>
    <el-pagination
      layout="prev, pager, next"
      :total="1000"
      style="height: 20px"
      class="pahe"
    >
    </el-pagination>
  </el-card>
</template>

<script>
import lineCharts from "./lineCharts";
export default {
  name: "Search",
  data() {
    return {
      tableData: [{}],
    };
  },
  components: {
    lineCharts,
  },
};
</script>

<style scoped>
.search-header {
  padding: 5px 0;
  display: flex;
  justify-content: space-between;

  border-bottom: 1px solid #eee;
}
.pahe {
  float: right;
  margin: 10px 0;
}
</style>