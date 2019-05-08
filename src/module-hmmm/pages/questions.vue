<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button type="primary" plain size="small">新增试题</el-button>
      <el-button type="primary" plain size="small">批量导入</el-button>
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="学科">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="试题类型">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="关键字">
          <el-input v-model="formInline.user" placeholder="请输入题目编号/题干"></el-input>
        </el-form-item>
        <el-form-item label="题目备注">
          <el-input v-model="formInline.user" placeholder="请输入"></el-input>
        </el-form-item>
        <el-form-item label="企业简称">
          <el-input v-model="formInline.user" placeholder="请输入"></el-input>
        </el-form-item>
        <el-form-item label="方向">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="录入人">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录">
          <el-input v-model="formInline.user" placeholder="请输入二级目录"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="clear">清除</el-button>
          <el-button type="primary" @click="onSubmit">查询</el-button>
        </el-form-item>
      </el-form>
      <el-table
        :data="tableData"
        border
        style="width: 100%">
        <el-table-column
          prop="date"
          label="序号"
          width="180">
        </el-table-column>
        <el-table-column
          prop="name"
          label="试题编号"
          width="180">
        </el-table-column>
        <el-table-column
          prop="address"
          label="学科">
        </el-table-column>
        <el-table-column
          prop="address"
          label="题型">
        </el-table-column>
        <el-table-column
          prop="address"
          label="录入时间">
        </el-table-column>
        <el-table-column
          prop="address"
          label="难度">
        </el-table-column>
        <el-table-column
          prop="address"
          label="使用次数">
        </el-table-column>
        <el-table-column
          prop="address"
          label="录入人">
        </el-table-column>
        <el-table-column
          prop="address"
          label="操作">
        </el-table-column>
      </el-table>
      <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage4"
        :page-sizes="[10, 20, 30, 40]"
        :page-size="10"
        layout="prev, pager, next, sizes, jumper"
        :total="400">
      </el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  name: 'QuestionsList',
  data() {
    return {
      formInline: {
          user: '',
          region: ''
      }
    }
  },
  methods: {
    onSubmit() {
      console.log('submit!');
    }
  }
}
</script>

<style scoped>
.el-form{
  margin-top: 10px;
}
.el-pagination{
  margin-top: 10px;
  float: right;
}
</style>
