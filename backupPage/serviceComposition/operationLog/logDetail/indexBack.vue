<template>
  <div class="common-layout">
    <el-card shadow="never">
      <template #header>
        <div class="card-header" style="">
          <el-tag>服务[service1]</el-tag>&nbsp;
          <b class="head-title" style="font-weight: 700">[接口描述] 操作日志详情</b>
        </div>
      </template>
      <el-tabs type="border-card" id="paneList">
        <el-tab-pane label="配置输入">
          <codemirror
            v-model="stringTest"
            placeholder="Code goes here..."
            :style="{ height: '400px' }"
            :autofocus="true"
            :indent-with-tab="true"
            :tab-size="2"
          />
        </el-tab-pane>
        <el-tab-pane label="JSON">
          <div class="" style="width: 400px; height: 200px">
            <vue-json-pretty v-model:data="jsonTest"></vue-json-pretty>
            <!-- <vue-json-pretty :path="'res'" :data="{ key: 'value' }"> </vue-json-pretty> -->
          </div>
        </el-tab-pane>

        <el-tab-pane label="基础信息">
          <el-tabs>
            <el-tab-pane>
              <template #label>
                <span class="custom-tabs-label">
                  <el-badge :value="12" class="badge-item"> comments </el-badge>
                </span>
              </template>
              comments
            </el-tab-pane>
            <el-tab-pane label="Config"> Config </el-tab-pane>
            <el-tab-pane label="配置步骤">配置步骤</el-tab-pane>
            <el-tab-pane label="配置输出">配置输出</el-tab-pane>
          </el-tabs>
          <!-- </el-card> -->
        </el-tab-pane>

        <el-tab-pane label="配置步骤">
          <formInput></formInput>
        </el-tab-pane>
        <el-tab-pane label="配置输出">
          <outTable></outTable>
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>
<script setup lang="ts">
declare module "sortablejs";
import { nextTick, onMounted, ref } from "vue";
import formInput from "./components/formInput.vue";
import outTable from "./components/outTable.vue";
import Sortable from "sortablejs";

import VueJsonPretty from "vue-json-pretty";
import "vue-json-pretty/lib/styles.css";

import { Codemirror } from "vue-codemirror";
// import { javascript } from '@codemirror/lang-javascript'
// import { oneDark } from '@codemirror/theme-one-dark'

const summaryOfFeedback = ref(0);
let stringTest = ref("");
let jsonTest = ref({
  name: "1",
  number: "2",
  key: "3",
  value: {
    test: 1,
  },
});
onMounted(() => {
  nextTick(() => {
    // let example1 = <any>document.getElementsByClassName('el-tabs__nav');
    let example1 = <any>document.querySelector("#paneList > div.el-tabs__header.is-top > div > div > div");
    // debugger
    new Sortable(example1, {
      animation: 150,
      ghostClass: "blue-background-class",
    });
  });
});
</script>
<style scoped lang="scss">
@import "./index";
</style>
