<template>
  <div class="queryParams">
    <el-table :data="tableData" stripe>
      <el-table-column prop="paramName" label="参数名">
        <template #default="scope">
          <el-input v-model="scope.row.paramName" />
        </template>
      </el-table-column>

      <el-table-column prop="paramValue" label="参数值">
        <template #default="scope">
          <el-input v-model="scope.row.paramValue" />
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
    paramName: "",
    paramValue: "",
  });
};
const deleteRow = (scope: any) => {
  tableData.value.splice(scope.$index, 1);
};
const tableData = ref<any>([
  {
    paramName: "",
    paramValue: "",
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
