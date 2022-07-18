<template>
  <div>
    <el-form ref="form" label-width="80px" :model="spuList">
      <el-form-item label="SPU名称">
        <el-input
          placeholder="请输入SPU名称"
          v-model="spuList.spuName"
        ></el-input>
      </el-form-item>
      <el-form-item label="品牌">
        <el-select v-model="spuList.tmId" placeholder="请选择品牌">
          <el-option
            :label="tm.tmName"
            :value="tm.id"
            v-for="(tm, inedx) in tradeMarkList"
            :key="tm.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="SPU描述">
        <el-input
          type="textarea"
          placeholder="请描述"
          rows="4"
          v-model="spuList.description"
        ></el-input>
      </el-form-item>
      <el-form-item label="SPU图片">
        <el-upload
          action="/dev-api/admin/product/fileUpload"
          list-type="picture-card"
          :on-preview="handlePictureCardPreview"
          :on-remove="handleRemove"
          :on-success="handleSuccess"
          :file-list="imgageList"
        >
          <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="" />
        </el-dialog>
      </el-form-item>
      <el-form-item label="销售属性">
        <el-select
          :placeholder="`还有${unSelectSaleAttr.length}未选择`"
          v-model="attrIdAndName"
        >
          <el-option
            :label="unselect.name"
            :value="`${unselect.id}:${unselect.name}`"
            v-for="unselect in unSelectSaleAttr"
            :key="unselect.id"
          ></el-option>
        </el-select>
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!attrIdAndName"
          @click="addAttrValue"
          >添加销售属性</el-button
        >
        <el-table border style="width: 100%" :data="spuList.spuSaleAttrList">
          <el-table-column
            type="index"
            label="序号"
            width="80px"
            align="center"
          >
          </el-table-column>
          <el-table-column prop="saleAttrName" label="属性名" width="width">
          </el-table-column>
          <el-table-column prop="prop" label="属性值名称列表" width="width">
            <template slot-scope="{ row, $index }">
              <!-- @close="handleClose(tag)" -->
              <el-tag
                :key="tag.id"
                v-for="(tag, index) in row.spuSaleAttrValueList"
                closable
                @close="row.spuSaleAttrValueList.splice(index, 1)"
                :disable-transitions="false"
              >
                {{ tag.saleAttrValueName }}
              </el-tag>

              <el-input
                class="input-new-tag"
                v-if="row.inputVisible"
                v-model="row.inputValue"
                ref="saveTagInput"
                size="small"
                @keyup.enter.native="handleInputConfirm(row)"
                @blur="handleInputConfirm(row)"
              >
              </el-input>

              <el-button
                v-else
                class="button-new-tag"
                size="small"
                @click="showInput(row)"
                >添加</el-button
              >
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template slot-scope="{ row, $index }">
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="mini"
                @click="spuList.spuSaleAttrList.splice($index, 1)"
              ></el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addOrUpdateSpu">保存</el-button>
        <el-button @click="cancel">取消 </el-button></el-form-item
      >
    </el-form>
  </div>
</template>

<script>
export default {
  name: "SpuForm",
  data() {
    return {
      dialogImageUrl: "",
      dialogVisible: false,
      spuList: {
        category3Id: 0,
        description: "",
        tmId: 0,
        spuImageList: [
          // {
          //   id: 0,
          //   imgName: "",
          //   imgUrl: "",
          //   spuId: 0,
          // },
        ],
        spuName: "",
        spuSaleAttrList: [
          // {
          //   baseSaleAttrId: 0,
          //   id: 0,
          //   saleAttrName: "",
          //   spuId: 0,
          //   spuSaleAttrValueList: [
          //     {
          //       baseSaleAttrId: 0,
          //       id: 0,
          //       isChecked: "",
          //       saleAttrName: "",
          //       saleAttrValueName: "",
          //       spuId: 0,
          //     },
          //   ],
          // },
        ],
      },
      tradeMarkList: [],
      baseList: [],
      imgageList: [],
      attrIdAndName: "",
    };
  },
  methods: {
    handleRemove(file, fileList) {
      console.log(file, fileList);
      this.imgageList = fileList;
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    handleSuccess(response, file, fileList) {
      console.log(response, file, fileList);
      this.imgageList = fileList;
      console.log(this.imgageList);
    },
    initSpuDate(spu) {
      this.$API.spu
        .reqSpu(spu.id)
        .then((res) => {
          this.spuList = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });

      this.$API.spu
        .reqTradeMark()
        .then((res) => {
          this.tradeMarkList = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });

      this.$API.spu
        .reqBaseSaleAttrList()
        .then((res) => {
          this.baseList = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });

      this.$API.spu
        .reqSpuImageList(spu.id)
        .then((res) => {
          let listAttr = res.data;
          listAttr.forEach((item) => {
            item.name = item.imgName;
            item.url = item.imgUrl;
          });
          // console.log(listAttr);
          this.imgageList = listAttr;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    addAttrValue() {
      const [baseSaleAttrId, saleAttrName] = this.attrIdAndName.split(":");
      const newAttr = {
        baseSaleAttrId,
        saleAttrName,
        spuSaleAttrValueList: [],
      };
      this.spuList.spuSaleAttrList.push(newAttr);
      this.attrIdAndName = "";
    },
    handleInputConfirm(row) {
      const { baseSaleAttrId, inputValue } = row;
      if (inputValue.trim() === "") {
        this.$message("属性值不能为空");
        return;
      }

      let result = row.spuSaleAttrValueList.every((item) => {
        return item.saleAttrValueName != inputValue;
      });
      if (!result) {
        this.$message("属性值内容不能重复");
        return;
      }
      console.log(result, "@@", row);
      const newAttr = { baseSaleAttrId, saleAttrValueName: inputValue };
      console.log(row, 11);
      row.spuSaleAttrValueList.push(newAttr);
      row.inputVisible = false;
    },
    showInput(row) {
      this.$set(row, "inputVisible", true);
      this.$set(row, "inputValue", "");
      this.$nextTick(() => {
        this.$refs.saveTagInput.focus();
      });
    },
    addOrUpdateSpu() {
      this.spuList.spuImageList = this.imgageList.forEach((item) => {
        item.imgageName = item.name;
        item.imgageUrl = (item.response && item.response.data) || item.url;
      });
      this.$API.spu
        .reqAAddOrUpdateSpu(this.spuList)
        .then((res) => {
          console.log(res);
          this.$message({ type: "success", message: "保存成功" });
          this.$emit("changeSence", {
            sence: 0,
            flag: this.spuList.id ? "修改" : "保存",
          });
        })
        .catch((err) => {
          console.log(err.message);
        });
      Object.assign(this._data, this.$options.data());
    },
    addSpuData(category3Id) {
      this.spuList.category3Id = category3Id;
      this.$API.spu
        .reqTradeMark()
        .then((res) => {
          this.tradeMarkList = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });

      this.$API.spu
        .reqBaseSaleAttrList()
        .then((res) => {
          this.baseList = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    cancel() {
      this.$emit("changeSence", { sence: 0, flag: "" });
      Object.assign(this._data, this.$options.data());
    },
  },
  computed: {
    unSelectSaleAttr() {
      const res = this.baseList.filter((item) => {
        return this.spuList.spuSaleAttrList.every((item1) => {
          return item.name != item1.saleAttrName;
        });
      });
      return res;
    },
  },
};
</script>
<style>
.el-tag + .el-tag {
  margin-left: 10px;
}
.button-new-tag {
  margin-left: 10px;
  height: 32px;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}
.input-new-tag {
  width: 90px;
  margin-left: 10px;
  vertical-align: bottom;
}
</style>