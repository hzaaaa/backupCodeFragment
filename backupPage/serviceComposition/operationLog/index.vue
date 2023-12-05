<!-- 操作日志 -->
<template>
  <div class="common-layout">
    <!-- name="height-fade" -->
    <Transition name="height-fade">
      <div class="needHide" style="" v-show="queryFormShow">
        <el-card class="is-always-shadow">
          <el-form :model="queryForm">
            <el-row>
              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="所属服务:">
                  <el-input v-model="queryForm.serviceName" placeholder="所属服务" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="接口名:">
                  <el-input v-model="queryForm.serviceDescription" placeholder="接口名" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="路径:">
                  <el-input v-model="queryForm.team" placeholder="路径" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="类型:">
                  <!-- <el-input v-model="queryForm.head" placeholder="类型" clearable></el-input> -->
                  <el-select v-model="queryForm.head" clearable placeholder="请选择">
                    <el-option v-for="item in headOptions" :key="item.value" :label="item.label" :value="item.value" />
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="12" style="padding: 0 10px">
                <el-form-item label="操作时间:">
                  <div class="picker-item">
                    <el-date-picker v-model="queryForm.dateInput.value1" type="datetime" placeholder="开始日期" />
                  </div>
                  <div class="separator">-</div>
                  <div class="picker-item">
                    <el-date-picker style="" v-model="queryForm.dateInput.value2" type="datetime" placeholder="结束日期" />
                  </div>
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
        </el-card>
        <div class="search-buttons">
          <el-button style="margin-right: 30px" :icon="Delete" @click="clearQueryForm">清空</el-button>
          <el-button style="margin-right: 20px" type="primary" @click="searchByQueryForm" :icon="Search">搜索</el-button>
        </div>
      </div>
    </Transition>
    <div class="operat-buttons">
      <!-- <el-button style="" type="primary" :icon="Plus">新增</el-button>
      <el-button style="" type="danger" :icon="Delete" plain>删除</el-button> -->
      <div class="space"></div>
      <!-- <el-tooltip
        
        effect="dark"
        content="刷新"
        placement="top"
      >
        <el-button style="" circle :icon="Refresh" @click="refreshTable"></el-button>
      </el-tooltip>
      <el-tooltip
        
        effect="dark"
        content="搜索"
        placement="top"
      >
        <el-button style="" circle :icon="Search" @click="queryFormHideOrShow"></el-button>
      </el-tooltip> -->
      <!-- <el-button style="" circle :icon="Refresh" @click="refreshTable"></el-button>
      <el-button style="" circle :icon="Search" @click="queryFormHideOrShow"></el-button> -->
    </div>
    <div class="table-block" v-loading="tableLoading">
      <el-table :data="tableDataList" border style="width: 100%" :max-height="tableMaxHeight">
        <!-- <el-table-column type="selection" width="50" /> -->
        <el-table-column type="index" width="50" label="#" header-align="center" align="center" />
        <el-table-column prop="date" label="所属服务" width="180" />
        <el-table-column prop="name" label="接口名" width="180" />
        <el-table-column prop="address" label="方法" />
        <el-table-column prop="address" label="路径" />
        <el-table-column prop="address" label="操作人" />
        <el-table-column prop="address" label="类型" />
        <el-table-column prop="address" label="操作时间" />
        <el-table-column prop="address" label="版本号" />
        <el-table-column label="操作" width="80px" header-align="center" align="center">
          <template #default="scope">
            <el-button link type="primary" size="small" :icon="View" @click="viewRowClick(scope.row)">查看</el-button>
            <!-- <el-button link type="primary" size="small" :icon="Delete">删除</el-button>
            <el-button link type="primary" size="small" :icon="Setting">权限</el-button> -->
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="pagination-block">
      <el-pagination
        :page-sizes="[10, 20, 30, 40]"
        background
        layout="total,sizes,prev, pager, next,jumper"
        @size-change="handleSizeChange"
        @current-change="handleCurrentPageChange"
        :current-page="pageParams.pageNum"
        :page-size="pageParams.pageSize"
        :total="pageParams.total"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from "vue";
import { Delete, Refresh, Search, Plus, EditPen, Setting, View } from "@element-plus/icons-vue";
import useListPageHook from "@/hooks/listPage.ts";
import router from "@/routers";

const viewRowClick = (row: any) => {
  console.log("viewRowClick", row);
  router.push("/serviceComposition/logDetail");
};

//#region  搜索相关
// 定义变量内容
const queryForm = ref({
  serviceName: "",
  serviceDescription: "",
  team: "",
  head: "",
  dateInput: {
    value1: "",
    value2: "",
  },
});
const queryFormShow = ref(true);
//清空按钮
const clearQueryForm = () => {
  //清空
  queryForm.value = {
    serviceName: "",
    serviceDescription: "",
    team: "",
    head: "",
    dateInput: {
      value1: "",
      value2: "",
    },
  };
  searchByQueryForm();
};
//搜索按钮
const searchByQueryForm = () => {
  //修改 搜索条件query
  setUseQueryParams(queryForm.value);
  //回到第一页
  resetPageToOne();
};

const headOptions = ref([
  {
    value: "1",
    label: "未发布",
  },
  {
    value: "2",
    label: "已发布",
  },
  {
    value: "3",
    label: "已下线",
  },
  {
    value: "4",
    label: "待审核",
  },
  {
    value: "5",
    label: "待发布",
  },
  {
    value: "6",
    label: "待下线",
  },
  {
    value: "7",
    label: "审核不通过",
  },
]);
//#endregion

//#region 表格相关

let createTableByData = (pageSize: number, pageNum: number) => {
  let list = [];
  while (pageSize--) {
    list.push({
      date: "2016-05-02",
      name: "Tom   " + pageNum,
      address: "No. 189, Grove St, Los Angeles",
    });
  }
  return list;
};

const getTableListApi = (params: any) => {
  console.log({ ...params });
  return new Promise((resolve, reject) => {
    setTimeout((_) => {
      resolve({
        data: {
          total: params.pageSize * 2,
          list: createTableByData(params.pageSize, params.pageNum),
        },
      });
    }, 500);
  });
};

const beforeQuery = () => {
  return queryForm.value;
};

let {
  tableLoading,
  tableMaxHeight,
  pageParams,
  tableDataList,
  handleCurrentPageChange,
  handleSizeChange,
  resetPageToOne,
  refreshData,
  setUseQueryParams,
} = useListPageHook(getTableListApi, () => {
  /*初始搜索条件query*/ return { ...queryForm.value };
});

//刷新按钮
const refreshTable = () => {
  //不修改任何 搜索条件query 直接查
  refreshData();
};
//hide or show
const queryFormHideOrShow = () => {
  queryFormShow.value = !queryFormShow.value;
};
//#endregion
</script>

<style lang="scss" scoped>
@import "./index";
</style>
