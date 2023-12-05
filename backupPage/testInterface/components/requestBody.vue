<template>
  <div class="requestBody">
    <el-radio-group v-model="activeName" class="ml-4">
      <el-radio label="first" size="large">Form-data</el-radio>
      <el-radio label="second" size="large">Raw</el-radio>
    </el-radio-group>
    <el-tabs v-model="activeName">
      <el-tab-pane name="first" label="Form-data">
        <el-table :data="tableData" stripe>
          <el-table-column prop="paramName" label="参数名">
            <template #default="scope">
              <el-input v-model="scope.row.paramName" />
            </template>
          </el-table-column>

          <el-table-column prop="paramType" label="参数值类型">
            <template #default="scope">
              <!-- @change="paramTypeChange(scope)" -->
              <el-select
                v-model="scope.row.paramType"
                @change="scope.row.paramValue = null"
                placeholder="请选择参数名"
                size="default"
              >
                <el-option v-for="item in paramsoOption" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </template>
          </el-table-column>
          <el-table-column prop="paramValue" label="参数值">
            <template #default="scope">
              <!-- <el-input v-model="input" placeholder="Please input" /> -->
              <el-input v-model="scope.row.paramValue" v-if="scope.row.paramType !== 'file'" />
              <el-input v-model="scope.row.paramValue" v-if="scope.row.paramType === 'file'" type="file" />
            </template>
          </el-table-column>
          <el-table-column label="操作">
            <template #default="scope">
              <el-button size="small" link type="primary" @click="addRow(scope)">增加行</el-button>
              <el-button size="small" link type="danger" @click="deleteRow(scope)" v-show="scope.$index !== 0">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <el-tab-pane name="second" label="Raw">
        <el-form>
          <el-form-item label="内容类型">
            <el-select v-model="contentType" style="margin-left: 10px" placeholder="内容类型" size="default">
              <el-option v-for="item in contentTypeOption" :key="item.value" :label="item.label" :value="item.value" />
            </el-select>
          </el-form-item>
        </el-form>
        <codemirror
          v-model="stringTest"
          placeholder="请输入..."
          :style="{ height: '200px' }"
          :autofocus="true"
          :indent-with-tab="true"
          :tab-size="2"
        />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { Codemirror } from "vue-codemirror";
const activeName = ref("first");
let stringTest = ref("");

const addRow = (scope: any) => {
  tableData.value.splice(scope.$index + 1, 0, {
    paramName: "",
    paramType: "string",
    paramValue: null,
  });
};
const deleteRow = (scope: any) => {
  tableData.value.splice(scope.$index, 1);
};
const paramTypeChange = (scope: any) => {
  console.log(scope);
};
const tableData = ref<any>([
  {
    paramName: "",
    paramType: "string",
    paramValue: null,
  },
]);
const paramsoOption = [
  {
    label: "string",
    value: "string",
  },
  {
    label: "file",
    value: "file",
  },
];
const contentType = ref("JSON");
const contentTypeOption = [
  {
    label: "JSON",
    value: "JSON",
  },
  {
    label: "XML",
    value: "XML",
  },
  {
    label: "HTML",
    value: "HTML",
  },
  {
    label: "Text",
    value: "Text",
  },
];
</script>

<style lang="scss" scoped>
:deep(.el-tabs__header) {
  display: none;
}
</style>
