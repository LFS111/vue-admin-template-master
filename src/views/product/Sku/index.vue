<template>
  <div>
    <el-table style="width: 100%" border :data="records">
      <el-table-column type="index" label="序号" width="80" align="center">
      </el-table-column>
      <el-table-column prop="skuName" label="名称" width="width">
      </el-table-column>
      <el-table-column prop="skuDesc" label="描述" width="width">
      </el-table-column>
      <el-table-column label="默认图片" width="110">
        <template slot-scope="{ row, $index }">
          <img
            :src="row.skuDefaultImg"
            alt=""
            style="width: 70px; height: 70px"
          />
        </template>
      </el-table-column>
      <el-table-column prop="weight" label="重量" width="80"> </el-table-column>
      <el-table-column prop="price" label="价格" width="80"> </el-table-column>
      <el-table-column label="操作" width="width">
        <template slot-scope="{ row, $index }">
          <el-button
            type="success"
            icon="el-icon-sort-down"
            size="mini"
            v-if="row.isSale == 0"
            @click="sale(row)"
          ></el-button>
          <el-button
            v-else
            type="success"
            icon="el-icon-sort-up"
            size="mini"
            @click="cancel(row)"
          ></el-button>
          <el-button
            type="primary"
            icon="el-icon-edit"
            size="mini"
            @click="edit"
          ></el-button>
          <el-button
            type="info"
            icon="el-icon-info"
            size="mini"
            @click="getSkuInfo(row)"
          ></el-button>
          <el-button
            type="danger"
            icon="el-icon-delete"
            size="mini"
          ></el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      style="text-align: center"
      :current-page="page"
      :page-sizes="[3, 5, 10]"
      :page-size="limit"
      layout="prev, pager, next, jumper,->,sizes, total"
      :total="total"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    >
    </el-pagination>
    <el-drawer :visible.sync="show" :show-close="false" size="50%">
      <el-row>
        <el-col :span="5">名称</el-col>
        <el-col :span="16">{{ skuInfo.skuName }} </el-col>
      </el-row>
      <el-row>
        <el-col :span="5">描述</el-col>
        <el-col :span="16">{{ skuInfo.skuDesc }} </el-col>
      </el-row>
      <el-row>
        <el-col :span="5">价格</el-col>
        <el-col :span="16">{{ skuInfo.price }}元 </el-col>
      </el-row>
      <el-row>
        <el-col :span="5">平台属性</el-col>
        <el-col :span="16"
          ><template>
            <el-tag
              type="success"
              v-for="(attr, index) in skuInfo.skuAttrValueList"
              style="margin-right: 10px"
              :key="attr.id"
              >{{ attr.attrId }}-{{ attr.valueId }}</el-tag
            >
          </template>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="5">商品图片</el-col>
        <el-col :span="16">
          <span class="demonstration">默认 Hover 指示器触发</span>
          <el-carousel height="150px">
            <el-carousel-item
              v-for="item in skuInfo.skuImageList"
              :key="item.id"
            >
              <img :src="item.imgUrl" alt="" />
            </el-carousel-item>
          </el-carousel>
        </el-col>
      </el-row>
    </el-drawer>
  </div>
</template>

<script>
export default {
  name: "Sku",
  data() {
    return {
      page: 1,
      limit: 5,
      records: [],
      total: 0,
      skuInfo: {},
      show: false,
    };
  },
  mounted() {
    this.getSkuList();
  },
  methods: {
    getSkuList() {
      const { page, limit } = this;
      this.$API.sku
        .reqSkuList(page, limit)
        .then((res) => {
          this.records = res.data.records;
          this.total = res.data.total;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    handleCurrentChange(page) {
      this.page = page;
      this.getSkuList();
    },
    handleSizeChange(limit) {
      this.limit = limit;
      this.getSkuList();
    },
    sale(sku) {
      this.$API.sku
        .reqOnSale(sku.id)
        .then((res) => {
          sku.isSale = 1;
          this.$message({ type: "success", message: "上架成功" });
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    cancel(sku) {
      this.$API.sku
        .reqOnSale(sku.id)
        .then((res) => {
          sku.isSale = 0;
          this.$message({ type: "success", message: "下架成功" });
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    edit() {
      this.$message("正在开发中");
    },
    getSkuInfo(sku) {
      this.show = true;
      this.$API.sku
        .reqSkuById(sku.id)
        .then((res) => {
          console.log(res);
          this.skuInfo = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
  },
};
</script>

<style>
.el-row .el-col-5 {
  font-size: 18px;
  text-align: right;
}
.el-col {
  margin: 10px 10px;
}
.el-carousel__item h3 {
  color: #475669;
  font-size: 14px;
  opacity: 0.75;
  line-height: 150px;
  margin: 0;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
}

.el-carousel__item:nth-child(2n + 1) {
  background-color: #d3dce6;
}
.el-carousel__button {
  width: 10px;
  height: 10px;
  background: red;
  border-radius: 50%;
}
</style>