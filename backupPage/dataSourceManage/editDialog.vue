<template>
  <div>
    <!-- :before-close="isClose" -->
    <el-dialog v-model="dialogVisible" :title="dialogTitle" width="1000px">
      <el-form :model="editFormShow" label-width="120px">
        <el-row>
          <el-col :span="12">
            <el-form-item label="资源代码:" align>
              <el-input v-model="editFormShow.resourceId" placeholder="资源代码" clearable></el-input>
            </el-form-item>
          </el-col>

          <el-col :span="12">
            <el-form-item label="资源名称:">
              <el-input v-model="editFormShow.resourceName" placeholder="资源名称" clearable></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="12">
            <el-form-item label="方法:">
              <el-select v-model="editFormShow.method" clearable placeholder="请选择" style="width: 100%">
                <el-option v-for="item in 3" :key="item" :label="item" :value="item" />
              </el-select>
            </el-form-item>
          </el-col>

          <el-col :span="12">
            <el-form-item label="是否签名:">
              <el-radio-group v-model="editFormShow.sign">
                <el-radio label="是" />
                <el-radio label="否" />
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="是否加密:">
              <el-radio-group v-model="editFormShow.encrypt">
                <el-radio label="是" />
                <el-radio label="否" />
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="标准化处理:">
              <el-checkbox-group v-model="editFormShow.standardization">
                <el-checkbox label="标准msg" name="标准msg" />
                <el-checkbox label="标准code" name="标准code" />
                <el-checkbox label="标准data" name="标准data" />
              </el-checkbox-group>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="content_type:">
              <el-select v-model="editFormShow.content_type" clearable placeholder="请选择" style="width: 100%">
                <el-option v-for="item in 3" :key="item" :label="item" :value="item" />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="协议转换:">
              <el-input
                type="textarea"
                :rows="5"
                v-model="editFormShow.protocolConversion"
                placeholder="协议转换"
                clearable
              ></el-input>
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
  resourceId: "",
  resourceName: "",
  method: "",
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
