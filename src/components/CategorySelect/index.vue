<template>
  <div>
    <el-form :inline="true" class="demo-form-inline" :model="cForm">
      <el-form-item label="一级表单">
        <el-select
          placeholder="请选择"
          v-model="cForm.category1Id"
          @change="handle1"
          :disabled="show || sence !== 0"
        >
          <el-option
            :label="c1.name"
            :value="c1.id"
            v-for="c1 in list1"
            :key="c1.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="二级表单">
        <el-select
          placeholder="请选择"
          v-model="cForm.category2Id"
          @change="handle2"
          :disabled="show || sence !== 0"
        >
          <el-option
            :label="c2.name"
            :value="c2.id"
            v-for="c2 in list2"
            :key="c2.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="三级表单">
        <el-select
          placeholder="请选择"
          v-model="cForm.category3Id"
          @change="handle3"
          :disabled="show || sence !== 0"
        >
          <el-option
            :label="c3.name"
            :value="c3.id"
            :key="c3.id"
            v-for="c3 in list3"
          ></el-option>
        </el-select>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: "CategorySelect",
  data() {
    return {
      list1: [],
      list2: [],
      list3: [],
      cForm: {
        category1Id: "",
        category2Id: "",
        category3Id: "",
      },
    };
  },
  props: ["show", "sence"],
  mounted() {
    this.getCategory1List();
    console.log(this);
  },
  methods: {
    getCategory1List() {
      this.$API.attr
        .reqCategory1List()
        .then((res) => {
          this.list1 = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    handle1() {
      this.list3 = [];
      this.list2 = [];
      this.cForm.category2Id = "";
      this.cForm.category3Id = "";

      const { category1Id } = this.cForm;
      this.$emit("getCategoryId", { categoryId: category1Id, level: 1 });
      this.$API.attr
        .reqCategory2List(category1Id)
        .then((res) => {
          this.list2 = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    handle2() {
      this.list3 = [];
      this.cForm.category3Id = "";

      const { category2Id } = this.cForm;
      this.$emit("getCategoryId", { categoryId: category2Id, level: 2 });
      this.$API.attr
        .reqCategory3List(category2Id)
        .then((res) => {
          this.list3 = res.data;
        })
        .catch((err) => {
          console.log(err.message);
        });
    },
    handle3() {
      const { category3Id } = this.cForm;
      this.$emit("getCategoryId", { categoryId: category3Id, level: 3 });
    },
  },
};
</script>

<style>
</style>