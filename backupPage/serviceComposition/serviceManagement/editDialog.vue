<template>
  <div>
    <!-- :before-close="isClose" -->
    <el-dialog v-model="dialogVisible" :title="dialogTitle" width="900px">
      <el-form :model="editFormShow" label-width="100px">
        <el-row>
          <el-col :span="12">
            <el-form-item label="服务名:" align>
              <el-input v-model="editFormShow.serviceName" placeholder="服务名" clearable></el-input>
            </el-form-item>
          </el-col>

          <el-col :span="12">
            <el-form-item label="服务描述:">
              <el-input v-model="editFormShow.serviceDescription" placeholder="服务描述" clearable></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <el-form-item label="团队:">
              <el-input v-model="editFormShow.team" placeholder="团队" clearable></el-input>
            </el-form-item>
          </el-col>

          <el-col :span="12">
            <el-form-item label="负责人:">
              <el-input v-model="editFormShow.head" placeholder="负责人" clearable></el-input>
              <!-- <el-select v-model="editFormShow.head" clearable placeholder="请选择"  >
                                    <el-option v-for="item in headOptions" :key="item.value" :label="item.label" :value="item.value" />
                                </el-select> -->
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="备注:">
              <el-input type="textarea" :rows="5" v-model="editFormShow.notes" placeholder="备注" clearable></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="dialogVisible = false">取消</el-button>
          <el-button type="primary" @click="submitClick"> 确定 </el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>
<script lang="ts" setup>
import { nextTick, ref } from "vue";
import { ElMessageBox } from "element-plus";

const dialogVisible = ref(false);
const dialogTitle = ref("");
const editFormShow = ref<any>({
  serviceId: null,
  serviceName: "",
  serviceDescription: "",
  team: "",
  head: "",
  notes: "",
});
const editFormRaw = JSON.parse(JSON.stringify(editFormShow.value));
let editFormQuery = {};
const openDialag = (data: any) => {
  if (data) {
    console.log("修改", data);
    modifyHandle(data);
  } else {
    console.log("新增");
    addHandle();
  }
  nextTick(() => {
    dialogVisible.value = true;
  });
};
const addHandle = () => {
  dialogTitle.value = "新增";
  editFormShow.value = JSON.parse(JSON.stringify(editFormRaw));
};
const modifyHandle = (data: any) => {
  dialogTitle.value = "编辑";
  beforeShowForm(data);
};
const beforeShowForm = (data: any) => {
  //库中数据=>展示数据
  editFormShow.value = JSON.parse(JSON.stringify(data));
};
const beforeSubmit = () => {
  //展示数据=>接口参数
  editFormQuery = JSON.parse(JSON.stringify(editFormShow.value));
};
const submitClick = () => {
  beforeSubmit();
  console.log("editFormQuery", editFormQuery);
  //提交后 关闭 刷新表格
};
defineExpose({
  openDialag,
});

const isClose = (done: () => void) => {
  ElMessageBox.confirm("Are you sure to close this dialog?")
    .then(() => {
      done();
    })
    .catch(() => {
      // catch error
    });
};
</script>
<style lang="scss" scoped>
:deep(.el-dialog) {
  .el-dialog__header {
    border-bottom: 1px solid #f0f0f0;
  }
}
.dialog-footer button:first-child {
  margin-right: 10px;
}
</style>
