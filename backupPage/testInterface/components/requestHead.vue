<template>
  <div class="requestHead">
    <el-table :data="tableData" stripe>
      <el-table-column prop="tag" label="标签">
        <template #default="scope">
          <el-select v-model="scope.row.tag" clearable placeholder="请选择标签" size="default">
            <el-option v-for="item in paramsoOption" :key="item.value" :label="item.label" :value="item.value" />
          </el-select>
        </template>
      </el-table-column>

      <el-table-column prop="content" label="内容">
        <template #default="scope">
          <el-input v-model="scope.row.content" />
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template #default="scope">
          <el-button size="small" link type="primary" @click="addRow(scope)">增加行</el-button>
          <el-button size="small" link type="danger" @click="deleteRow(scope)" v-show="scope.$index !== 0">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
const addRow = (scope: any) => {
  tableData.value.splice(scope.$index + 1, 0, {
    tag: "",
    content: "",
  });
};
const deleteRow = (scope: any) => {
  tableData.value.splice(scope.$index, 1);
};
const tableData = ref<any>([
  {
    tag: "",
    content: "",
  },
]);
const paramsoOption = [
  {
    label: "label",
    value: "value",
  },
];
</script>

<style lang="scss" scoped></style>
