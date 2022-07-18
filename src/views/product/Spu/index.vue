<template>
  <div>
    <el-card>
      <CategorySelect
        :show="!show"
        :sence="sence"
        @getCategoryId="getCategoryId"
    /></el-card>
    <el-card style="margin: 20px 0">
      <div v-show="sence === 0">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="addSpu"
          >添加spu</el-button
        >
        <el-table style="width: 100%" border :data="records">
          <el-table-column prop="prop" label="序号" width="80" align="center">
          </el-table-column>
          <el-table-column prop="spuName" label="spu名称" width="width">
          </el-table-column>
          <el-table-column prop="description" label="spu描述" width="width">
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row, $index }">
              <hint-button
                type="success"
                icon="el-icon-plus"
                size="mini"
                title="添加spu"
                @click="addSku(row)"
              ></hint-button>
              <hint-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                title="修改spu"
                @click="updateSpu(row)"
              ></hint-button>
              <hint-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看当前spu的全部sku列表"
                @click="handler(row)"
              ></hint-button>
              <el-popconfirm
                title="这是一段内容确定删除吗？"
                @onConfirm="deleteSpu(row)"
              >
                <hint-button
                  slot="reference"
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  title="删除spu"
                ></hint-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <!--
          -->
        <el-pagination
          :current-page="page"
          :page-sizes="[3, 5, 10]"
          :page-size="limit"
          layout="  prev, pager, next, jumper,->,sizes,total"
          :total="total"
          style="text-align: center"
          @current-change="handleCurrentChange"
          @size-change="handleSizeChange"
        >
        </el-pagination>
      </div>
      <SpuForm v-show="sence === 1" @changeSence="changeSence" ref="spu" />
      <SkuForm v-show="sence === 2" @changeSences="changeSences" ref="sku" />
    </el-card>
    <el-dialog
      :title="`${spu.spuName}的sku列表`"
      :visible.sync="dialogTableVisible"
      :before-close="close"
    >
      <el-table :data="skuList" border v-loading="loading">
        <el-table-column
          property="skuName"
          label="名称"
          width="150"
        ></el-table-column>
        <el-table-column
          property="price"
          label="价格"
          width="200"
        ></el-table-column>
        <el-table-column property="weight" label="重量"></el-table-column>
         <el-table-column  label="默认图片">
          <template slot-scope="{row,$index}"> 
            <img :src="row.skuDefaultImg" alt="" style="width:100px;height:100px ">
          </template>
         </el-table-column>
      </el-table>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>
import SkuForm from "@/views/product/Spu/SkuForm";
import SpuForm from "@/views/product/Spu/SpuForm";
export default {
  name: "Spu",
  components: {
    SkuForm,
    SpuForm,
  },
  data() {
    return {
      loading: true,
      category1Id: "",
      category2Id: "",
      category3Id: "",
      show: true,
      page: 1,
      sence: 0,
      limit: 3,
      total: 0,
      records: [],
      dialogTableVisible: false,
      spu: {},
      skuList: [],
    };
  },
  methods: {
    getCategoryId({ categoryId, level }) {
      if (level === 1) {
        this.category1Id = categoryId;
        this.category2Id = "";
        this.category3Id = "";
      } else if (level === 2) {
        this.category2Id = categoryId;
        this.category3Id = "";
      } else {
        this.category3Id = categoryId;
        this.getSpuList();
      }
    },
    getSpuList() {
      const { page, limit, category3Id } = this;
      this.$API.spu
        .reqSpuList(page, limit, category3Id)
        .then((res) => {
          this.total = res.data.total;
          this.records = res.data.records;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    handleCurrentChange(page) {
      this.page = page;
      this.getSpuList();
    },
    handleSizeChange(limit) {
      this.limit = limit;
      this.getSpuList();
    },
    addSpu() {
      this.sence = 1;
      this.$refs.spu.addSpuData(this.category3Id);
    },
    updateSpu(row) {
      this.sence = 1;
      this.$refs.spu.initSpuDate(row);
    },
    changeSence({ sence, flag }) {
      this.sence = sence;
      if (flag == "修改") {
        this.getSpuList(this.page);
      }
    },
    deleteSpu(row) {
      this.$API.spu
        .reqDeleteSpu(row.id)
        .then((res) => {
          console.log(res);
          this.$message({ type: "success", message: "删除成功" });
          this.getSpuList(this.records.length > 1 ? this.page : this.page - 1);
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    addSku(row) {
      this.sence = 2;
      this.$refs.sku.getSkuData(this.category1Id, this.category2Id, row);
    },
    changeSences(sence) {
      this.sence = sence;
    },
    handler(spu) {
      this.dialogTableVisible = true;
      this.spu = spu;
      this.$API.spu
        .reqSkuList(spu.id)
        .then((res) => {
          this.skuList = res.data;
          this.loading = false;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    close(done) {
      this.skuList = [];
      this.loading = true;
      done();
    },
  },
};
</script>

<style>
</style>