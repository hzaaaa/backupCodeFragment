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
                <el-form-item label="服务名:">
                  <el-input v-model="queryForm.serviceName" placeholder="服务名" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="服务描述:">
                  <el-input v-model="queryForm.serviceDescription" placeholder="服务描述" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="团队:">
                  <el-input v-model="queryForm.team" placeholder="团队" clearable></el-input>
                </el-form-item>
              </el-col>

              <el-col :span="6" style="padding: 0 10px">
                <el-form-item label="负责人:">
                  <el-input v-model="queryForm.head" placeholder="负责人" clearable></el-input>
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
        :max-height="tableMaxHeight"
        @selection-change="handleSelectionChange"
        ref="multipleTableRef"
      >
        <el-table-column type="selection" width="50" header-align="center" align="center" />
        <el-table-column type="index" width="50" label="#" header-align="center" align="center" />
        <el-table-column prop="serviceName" label="服务名">
          <template #default="scope">
            <!-- <el-button link type="primary" size="small" >{{scope.row.serviceName}}</el-button> -->
            <el-link type="primary">{{ scope.row.serviceName }}</el-link>
          </template>
        </el-table-column>
        <el-table-column prop="serviceDescription" label="服务描述" />
        <el-table-column prop="team" label="团队" />
        <el-table-column prop="head" label="负责人" />
        <el-table-column prop="notes" label="备注" />
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
    `确定要将${row.serviceName}数据删除吗?`,
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
  serviceName: "",
  serviceDescription: "",
  team: "",
  head: "",
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

// const headOptions = ref([
//   {
//     value: "1",
//     label: "未发布",
//   },
//   {
//     value: "2",
//     label: "已发布",
//   },
//   {
//     value: "3",
//     label: "已下线",
//   },
//   {
//     value: "4",
//     label: "待审核",
//   },
//   {
//     value: "5",
//     label: "待发布",
//   },
//   {
//     value: "6",
//     label: "待下线",
//   },
//   {
//     value: "7",
//     label: "审核不通过",
//   },
// ]);
//#endregion

//#region 表格 查 相关

let createTableByData = (pageSize: number, pageNum: number) => {
  let list = [];
  while (pageSize--) {
    list.push({
      serviceId: 0,
      serviceName: "serviceName",
      serviceDescription: "serviceDescription",
      team: "team",
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
