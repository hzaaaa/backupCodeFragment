<!-- 服务管理 -->
<template>
  <div class="common-layout">
    <!-- name="height-fade" -->
    <Transition name="height-fade">
      <div class="needHide" style="" v-show="queryFormShow">
        <el-card class="is-always-shadow">
          <el-form :model="queryForm">
            <el-row>
              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="资源代码:">
                  <el-input v-model="queryForm.resourceId" placeholder="资源代码" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="资源名称:">
                  <el-input v-model="queryForm.resourceName" placeholder="资源名称" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="方法:">
                  <el-input v-model="queryForm.method" placeholder="方法" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="是否签名:">
                  <el-input v-model="queryForm.head" placeholder="是否签名" clearable></el-input>
                  <!-- <el-select v-model="queryForm.head" clearable placeholder="请选择"  >
								<el-option v-for="item in headOptions" :key="item.value" :label="item.label" :value="item.value" />
							</el-select> -->
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
        </el-card>
        <div class="search-buttons">
          <el-button style="margin-right: 30px" :icon="Delete" @click="clearQueryForm">清空</el-button>
          <el-button style="margin-right: 20px" type="primary" @click="searchByQueryForm" :icon="Search">搜索</el-button>
          <!-- <el-button style="margin-right: 20px;" type="primary" @click="searchByQueryForm" :icon="Search">新增</el-button> -->
        </div>
      </div>
    </Transition>
    <div class="operat-buttons">
      <el-button style="" type="primary" :icon="Plus" @click="editDialogRef?.openDialag(false)">新增</el-button>
      <el-button style="" type="danger" :icon="Delete" @click="deleteBatch" plain>删除</el-button>
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
    </div>
    <div class="table-block" v-loading="tableLoading">
      <el-table
        :data="tableDataList"
        border
        style="width: 100%"
        @selection-change="handleSelectionChange"
        :max-height="tableMaxHeight"
        ref="multipleTableRef"
      >
        <el-table-column type="selection" width="50" header-align="center" align="center" />
        <el-table-column type="index" width="50" label="#" header-align="center" align="center" />
        <el-table-column prop="resourceId" label="资源代码">
          <template #default="scope">
            <!-- <el-button link type="primary" size="small" >{{scope.row.resourceId}}</el-button> -->
            <el-link type="primary">{{ scope.row.resourceId }}</el-link>
          </template>
        </el-table-column>
        <el-table-column prop="resourceName" label="资源名称" />
        <el-table-column prop="method" label="方法" />
        <el-table-column prop="head" label="是否签名" />
        <el-table-column prop="head" label="是否加密" />
        <el-table-column prop="head" label="是否标准化处理" />
        <el-table-column prop="head" label="协议转换" />
        <!-- <el-table-column prop="notes" label="备注" /> -->
        <el-table-column label="操作" width="240px" header-align="center" align="center">
          <template #default="scope">
            <el-button link type="primary" size="small" :icon="EditPen" @click="editDialogRef?.openDialag(scope.row)"
              >编辑</el-button
            >
            <el-button link type="primary" size="small" :icon="Delete" @click="deleteRowData(scope.row)">删除</el-button>
            <el-button link type="primary" size="small" :icon="Setting" @click="editDialogRef?.openDialag(scope.row)"
              >权限</el-button
            >
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

    <editDialog ref="editDialogRef"></editDialog>
  </div>
</template>

<script setup lang="ts">
import editDialog from "./editDialog.vue";
import { ref, reactive } from "vue";
import { Delete, Refresh, Search, Plus, EditPen, Setting } from "@element-plus/icons-vue";
import useListPageHook from "@/hooks/listPage";

import { ElMessage, ElMessageBox } from "element-plus";

//#region 表格 增删改 相关
const multipleTableRef = ref(null);
const multipleSelection = ref<any>([]);
const editDialogRef = <any>ref(null);

const handleSelectionChange = (val: any) => {
  multipleSelection.value = val;
};
const deleteBatch = () => {
  console.log("deleteBatch", multipleSelection.value);
};

const deleteRowData = (row: any) => {
  console.log("delete", row);
  ElMessageBox.confirm(
    `确定要将${row.resourceId}数据删除吗?`,
    // 'Warning',
    {
      confirmButtonText: "确定",
      cancelButtonText: "取消",
      type: "warning",
    }
  )
    .then(() => {
      console.log("delete success", row);
      ElMessage({
        type: "success",
        message: "删除成功",
      });
    })
    .catch(() => {
      // ElMessage({
      //   type: 'info',
      //   message: 'Delete canceled',
      // })
    });
};

//#endregion

//#region  搜索相关
// 定义变量内容
const queryForm = ref({
  resourceId: "",
  resourceName: "",
  method: "",
  head: "",
});
const queryFormShow = ref(true);
//清空按钮
const clearQueryForm = () => {
  //清空
  queryForm.value = {
    resourceId: "",
    resourceName: "",
    method: "",
    head: "",
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

//#endregion

//#region 表格 查 相关

let createTableByData = (pageSize: number, pageNum: number) => {
  let list = [];
  while (pageSize--) {
    list.push({
      serviceId: 0,
      resourceId: "resourceId",
      resourceName: "resourceName",
      method: "method",
      head: "head",
      notes: "notes" + pageNum,
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
