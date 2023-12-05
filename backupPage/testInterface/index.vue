<template>
  <div class="testInterface">
    <div class="head">
      <div class="pageName">测试</div>
      <!-- <div >返回</div> -->
      <el-link class="return" :underline="false" href="/#/partnerDetails" type="primary">返回</el-link>
    </div>
    <div class="content">
      <div class="form">
        <el-input v-model="testValue" placeholder="Please input" class="input-with-select">
          <template #prepend>
            <div style=" display: flex;margin: 0 -20px">
              <el-select v-model="testValue" placeholder="Select" style="margin: 0">
                <el-option label="HTTP" value="1" />
                <el-option label="HTTPS" value="2" />
              </el-select>
              <el-select v-model="testValue" placeholder="Select" style="margin: 0">
                <el-option label="POST" value="1" />
                <el-option label="GET" value="2" />
              </el-select>
            </div>
          </template>
        </el-input>
        <el-button style="margin: 0 30px" type="primary">测试</el-button>
      </div>

      <div class="request">
        <el-tabs v-model="activeName">
          <el-tab-pane name="first" label="请求头部">
            <requestHead></requestHead>
          </el-tab-pane>
          <el-tab-pane name="second" label="请求体">
            <requestBody></requestBody>
          </el-tab-pane>
          <el-tab-pane name="third" label="Query参数">
            <queryParams></queryParams>
          </el-tab-pane>
        </el-tabs>
      </div>
      <div class="responds">
        <el-card class="box-card is-always-shadow">
          <template #header>
            <div class="card-header">
              <span>测试结果</span>
            </div>
          </template>
          <vue-json-pretty v-model:data="jsonTest"></vue-json-pretty>
        </el-card>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import requestHead from "./components/requestHead.vue";
import queryParams from "./components/queryParams.vue";
import requestBody from "./components/requestBody.vue";

import VueJsonPretty from "vue-json-pretty";
import "vue-json-pretty/lib/styles.css";

let jsonTest = ref({
  name: "1",
  number: "2",
  key: "3",
  value: {
    test: 1,
  },
});

const testValue = ref<any>(null);
const activeName = ref("second");
</script>

<style lang="scss" scoped>
@import "./index";
</style>
