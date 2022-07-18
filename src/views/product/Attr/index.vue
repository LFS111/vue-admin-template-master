<template>
  <div>
    <el-card style="margin: 20px 0">
      <CategorySelect
        :show="!isShowTable"
        @getCategoryId="getCategoryId" /></el-card
    ><el-card>
      <div v-show="isShowTable">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="addAttr"
          >添加属性</el-button
        >
        <el-table style="width: 100%" border :data="attrList">
          <el-table-column type="index" label="序号" width="80" align="center">
          </el-table-column>
          <el-table-column
            prop="attrName"
            label="属性名称"
            width="width"
            align="center"
          >
          </el-table-column>
          <el-table-column
            prop="prop"
            label="属性值列表"
            width="width"
            align="center"
          >
            <template slot-scope="{ row, $index }">
              <el-tag
                type="success"
                v-for="(attrvalue, index) in row.attrValueList"
                :key="attrvalue.id"
                style="margin: 0px 10px"
                >{{ attrvalue.valueName }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="150" align="center">
            <template slot-scope="{ row, $index }">
              <el-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                @click="updateAttr(row)"
              ></el-button>
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="mini"
              ></el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div v-show="!isShowTable">
        <el-form ref="form" label-width="80px" :model="attrInfo">
          <el-form-item label="属性名">
            <el-input
              placeholder="请输入属性名"
              style="width: 200px"
              v-model="attrInfo.attrName"
            ></el-input>
          </el-form-item>
        </el-form>
        <el-button
          type="primary"
          icon="el-icon-plus"
          @click="addAttrValue"
          :disabled="!attrInfo.attrName"
          >添加属性值</el-button
        >
        <el-button @click="isShowTable = true">取消</el-button>
        <el-table
          style="width: 100%; margin: 20px 0"
          border
          :data="attrInfo.attrValueList"
        >
          <el-table-column type="index" label="序号" width="80" align="center">
          </el-table-column>
          <el-table-column prop="prop" label="属性值名称" width="width">
            <template slot-scope="{ row, $index }">
              <el-input
                v-model="row.valueName"
                placeholder="请输入属性名"
                size="mini"
                v-if="row.flag"
                @blur="changeFlag(row)"
                @keyup.native.enter="changeFlag(row)"
                :ref="$index"
              ></el-input>
              <span
                v-else
                @click="changeEdit(row, $index)"
                style="display: block"
                >{{ row.valueName }}</span
              >
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row, $index }">
              <el-popconfirm
                :title="`您确定删除${row.valueName}吗？`"
                @onConfirm="deleteAttrValue($index)"
              >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  slot="reference"
                ></el-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-button
          type="primary"
          @click="addOrUpdateAttr"
          :disabled="attrInfo.attrValueList.length < 1"
          >保存</el-button
        >
        <el-button @click="isShowTable = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
import cloneDeep from "lodash/cloneDeep";
export default {
  name: "Attr",
  data() {
    return {
      category1Id: "",
      category2Id: "",
      category3Id: "",
      attrList: [],
      isShowTable: true,
      attrInfo: {
        attrName: "",
        attrValueList: [],
        categoryId: 0,
        categoryLevel: 3,
      },
    };
  },
  methods: {
    getCategoryId({ categoryId, level }) {
      if (level == 1) {
        this.category1Id = categoryId;
        this.category2Id = "";
        this.category3Id = "";
      } else if (level == 2) {
        this.category2Id = categoryId;
        this.category3Id = "";
      } else {
        this.category3Id = categoryId;
        //发请求
        this.getAttrList();
      }
    },
    getAttrList() {
      const { category1Id, category2Id, category3Id } = this;
      this.$API.attr
        .reqAttrList(category1Id, category2Id, category3Id)
        .then((res) => {
          this.attrList = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    addAttrValue() {
      this.attrInfo.attrValueList.push({
        attrId: this.attrInfo.id,
        valueName: "",
        flag: true,
      });
      this.$nextTick(() => {
        this.$refs[this.attrInfo.attrValueList.length - 1].focus();
      });
    },
    addAttr() {
      this.isShowTable = false;
      this.attrInfo = {
        attrName: "",
        attrValueList: [],
        categoryId: this.category3Id,
        categoryLevel: 3,
      };
    },
    updateAttr(row) {
      // console.log(row);
      this.isShowTable = false;
      this.attrInfo = cloneDeep(row);
      this.attrInfo.attrValueList.forEach((item) =>
        this.$set(item, "flag", false)
      );
    },
    changeFlag(row) {
      if (row.valueName.trim() === "") {
        this.$message("内容不能为空");
        return;
      }

      const xx = this.attrInfo.attrValueList.some((item) => {
        if (row !== item) {
          return item.valueName === row.valueName;
        }
      });
      if (xx) {
        this.$message("内容已存在，不能重复！");
        return;
      }
      row.flag = false;
    },
    changeEdit(row, index) {
      row.flag = true;
      this.$nextTick(() => {
        this.$refs[index].focus();
      });
    },
    deleteAttrValue(index) {
      //console.log(row);
      // this.$API.attr.reqDeleteAttr(row.id);
      //  this.getAttrList();
      //  alert(1);
      this.attrInfo.attrValueList.splice(index, 1);
    },
    addOrUpdateAttr() {
      this.attrInfo.attrValueList = this.attrInfo.attrValueList.filter(
        (item) => {
          if (item.valueName != "") {
            delete item.flag;
            return true;
          }
        },
        this.$API.attr
          .reqAddOrUpdateAttr(this.attrInfo)
          .then((res) => {
            this.isShowTable = true;
            this.$message({ type: "success", message: "保存成功" });
            this.getAttrList();
          })
          .catch((err) => {
            console.log(err.message);
          })
      );
    },
  },
};
</script>

<style>
</style>