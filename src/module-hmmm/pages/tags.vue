<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>学科管理</el-breadcrumb-item>
        <el-breadcrumb-item>{{subjectName}}</el-breadcrumb-item>
        <el-breadcrumb-item>标签管理</el-breadcrumb-item>
      </el-breadcrumb>
      <el-button plain @click="addTag()" class="btn">新建标签</el-button>
      <el-button plain @click="lastSubjects()" class="btn">返回学科</el-button>
      <el-row :gutter="20" class="inputHead">
        <el-col :span="3">
          <el-input placeholder="请输入内容" :span="3" v-model="tagsParams.tagName"></el-input>
        </el-col>
        <el-col :span="12">
          <el-button @click="clearSearch()">清除</el-button>
          <el-button type="success" @click="objectsSearch()">搜索</el-button>
        </el-col>
      </el-row>
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column prop="id" label="序号"></el-table-column>
        <el-table-column prop="tagName" label="标签名称"></el-table-column>
        <el-table-column prop="creator" label="创建者"></el-table-column>
        <el-table-column prop="addDate" label="创建日期"></el-table-column>
        <el-table-column prop="totals" label="面试题数量"></el-table-column>
        <el-table-column label="状态" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.state">启用</el-tag>
            <el-tag v-else>禁用</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="300px">
          <template slot-scope="scope">
            <el-button :disabled="!!scope.row.state" type="primary" plain @click="editTag(scope.row)">修改</el-button>
            <el-button v-if="!scope.row.state" type="primary" plain @click="changeState(scope.row)">启用</el-button>
            <el-button v-else type="primary" plain @click="changeState(scope.row)">禁用</el-button>
            <el-button :disabled="!!scope.row.state" type="primary" plain @click="delTag(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        background
        layout="prev, pager, next"
        @current-change="changePager"
        :page-size="tagsParams.pagesize"
        :current-page="tagsParams.page"
        :total="total"
      ></el-pagination>
      <el-dialog width="400px" title="新建标签" :visible.sync="addTagDialogFormVisible">
      <el-form
        ref="addForm"
        :model="addForm"
        :rules="addRules"
        label-width="80px"
        autocomplete="off"
      >
        <el-form-item label="标签名称" prop="tagName">
          <el-input v-model="addForm.tagName"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addTagDialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addSubmit()">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog width="400px" title="修改标签" :visible.sync="editTagDialogFormVisible">
      <el-form
        ref="editForm"
        :model="editForm"
        :rules="editRules"
        label-width="80px"
        autocomplete="off"
      >
        <el-form-item label="目录名称" prop="tagName">
          <el-input v-model="editForm.tagName"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editTagDialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="editSubmit()">确 定</el-button>
      </div>
    </el-dialog>
    </div>
  </div>
</template>

<script>
import { list, simple, detail, add, update, remove, state } from '@/api/hmmm/tags'
export default {
  name: 'DirectorysList',
  data() {
    return {
      addTagDialogFormVisible:false,
      addForm:{
        subjectID:0,
        tagName:''
      },
      addRules: {
        tagName: [
          { required: true, message: '目录名必填!', trigger: 'blur' }
        ]
      },
      editTagDialogFormVisible:false,
      editForm:{
        id:0,
        subjectID:this.$route.query.subjectID,
        tagName:''
      },
      editRules: {
        tagName: [
          { required: true, message: '目录名必填!', trigger: 'blur' }
        ]
      },
      subjectName:this.$route.query.subjectName,
      tableData:[],
      total:0,     
      tagsParams: {
        page: 1,
        pagesize: 10,
        tagName: '',
        state: 0,
        subjectID: this.$route.query.subjectID
      },
    }
  },
  methods:{
    clearSearch() {
      this.tagsParams.tagName = ''
      this.nowstate = ''
    },
    objectsSearch() {
      this.getList()
      this.tagsParams.tagName = ''
    },
    async changeState(row){
      const rowState = !row.state
      const data = await state(row.id,rowState)
      if(data.status==200) {
        this.$message.success('修改状态成功')
        this.getList()
      }   
      else this.$message.error('修改状态失败')
    },
    delTag(id) {
      this.$confirm('此操作将永久删除标签 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await remove(id)
            .then(response => {
              this.$message.success('成功删除了标签' + '!')
              this.getList()
            })
            .catch(response => {
              this.$message.error('删除失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    },
    lastSubjects(){
      this.$router.push({path:'/subjects/list'})
    },
    editTag(row) {
      this.editTagDialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.editForm.resetFields()
        this.editForm.tagName = row.tagName
      })
    },
    addTag() {
      this.addTagDialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.addForm.resetFields()
      })
    },
    async addSubmit() {
      // 数据有问题 等待修正
      this.$refs.addForm.validate(async valid => {
        if (valid) {
          const data = await add(this.addForm)
          if(data.status==200){
            this.$message.success('添加成功!')
            this.addTagDialogFormVisible = false
            this.getList()
          }
          else this.$message.error('添加失败!')
        }
      })
    },
    async editSubmit() {
      // 数据有问题 等待修正
      this.$refs.editForm.validate(async valid => {
        if (valid) {
          const data = await update(this.editForm)
          if(data.status==200){
            this.$message.success('修改成功!')
            this.editTagDialogFormVisible = false
            this.getList()
          }
          else this.$message.error('修改失败!')
        }
      })
    },
    async getList(){
      const { data: res } = await list(this.tagsParams)
      this.tableData = res.items
      this.total = res.counts
    },
    changePager(newPage){
      this.tagsParams.page = newPage
      this.getList()
    }
  },
  mounted(){
    this.getList()
  }
}
</script>

<style scoped>
.btn{
  margin-top: 20px
}
.inputHead {
  margin: 10px 0;
}
.inputHead .el-button {
  margin-left: 10px;
}
.el-pagination {
  margin-top: 20px;
}
</style>

