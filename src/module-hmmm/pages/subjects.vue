<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button plain @click="addSubject()">新增学科</el-button>
      <el-row :gutter="20" class="inputHead">
        <el-col :span="6">
          <el-input v-model="subjectsParams.subjectName" placeholder="请输入学科名称" :span="6"></el-input>
        </el-col>
        <el-col :span="8" :offset="10">
          <el-button type="success" @click="objectsSearch()">搜索</el-button>
          <el-button @click="clearSearch()">清除</el-button>
        </el-col>
      </el-row>
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column prop="id" label="序号"></el-table-column>
        <el-table-column prop="subjectName" label="学科名称"></el-table-column>
        <el-table-column prop="creator" label="创建者"></el-table-column>
        <el-table-column prop="addDate" label="创建日期"></el-table-column>
        <el-table-column label="前台是否显示" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.isFrontDisplay">是</el-tag>
            <el-tag v-else>否</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="二级目录"></el-table-column>
        <el-table-column prop="tags" label="标签"></el-table-column>
        <el-table-column prop="totals" label="题目数量"></el-table-column>
        <el-table-column label="操作" width="400px">
          <template slot-scope="scope">
            <el-button type="primary" plain @click="directorys(scope.row)">二级目录</el-button>
            <el-button type="primary" plain @click="tags(scope.row)">学科标签</el-button>
            <el-button type="primary" plain @click="editSubject(scope.row)">修改</el-button>
            <el-button type="primary" plain @click="delSubject(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        background
        layout="prev, pager, next"
        @current-change="changePager"
        :page-size="subjectsParams.pagesize"
        :current-page="subjectsParams.page"
        :total="total"
      ></el-pagination>
    </div>
    <el-dialog width="400px" title="新增学科" :visible.sync="dialogFormVisible">
      <el-form
        ref="addForm"
        :model="addForm"
        :rules="addRules"
        label-width="80px"
        autocomplete="off"
      >
        <el-form-item label="新增学科" prop="subjectName">
          <el-input v-model="addForm.subjectName"></el-input>
        </el-form-item>
        <el-form-item label="是否显示" prop="isFrontDisplay">
          <el-switch
            v-model="addForm.isFrontDisplay"
            active-color="#13ce66"
            inactive-color="#ff4949"
          ></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addSubmit()">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog width="400px" title="修改学科" :visible.sync="editIndexDialogFormVisible">
      <el-form
        ref="editForm"
        :model="editForm"
        :rules="editRules"
        label-width="80px"
        autocomplete="off"
      >
        <el-form-item label="目录名称" prop="subjectName">
          <el-input v-model="editForm.subjectName"></el-input>
        </el-form-item>
        <el-form-item label="是否显示" prop="isFrontDisplay">
          <el-switch
            v-model="editForm.isFrontDisplay"
            active-color="#13ce66"
            inactive-color="#ff4949"
          ></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editIndexDialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="editSubmit()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { list, simple, detail, add, update, remove } from '@/api/hmmm/subjects'
export default {
  name: 'SubjectsList',
  data() {
    return {
      editIndexDialogFormVisible: false,
      editForm: {
        id: 0,
        isFrontDisplay: true,
        subjectName: ''
      },
      editRules: {
        subjectName: [
          { required: true, message: '学科名必填!', trigger: 'blur' }
        ]
      },
      addForm: {
        subjectName: '',
        isFrontDisplay: true
      },
      addRules: {
        subjectName: [
          { required: true, message: '学科名必填!', trigger: 'blur' }
        ]
      },
      total: 0,
      dialogFormVisible: false,
      subjectsParams: {
        page: 1,
        pagesize: 10,
        subjectName: ''
      },
      tableData: []
    }
  },
  methods: {
    directorys(row) {
      this.$router.push({
        path:
          'directorys/?subjectID=' + row.id + '&subjectName=' + row.subjectName
      })
    },
    tags(row) {
      this.$router.push({
        path: 'tags/?subjectID=' + row.id + '&subjectName=' + row.subjectName
      })
    },
    delSubject(id) {
      this.$confirm('此操作将永久删除学科 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await remove(id)
            .then(response => {
              this.$message.success('成功删除了学科' + '!')
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
    async editSubmit() {
      // 数据有问题 等待修正
      this.$refs.editForm.validate(async valid => {
        if (valid) {
          const data = await add(this.editForm)
          if(data.status==200){
            this.$message.success('修改成功!')
            this.editIndexDialogFormVisible = false
            this.getList()
          }
          else this.$message.error('修改失败!')
        }
      })
    },
    async addSubmit() {
      // 数据有问题 等待修正
      this.$refs.addForm.validate(async valid => {
        if (valid) {
          const data = await add(this.addForm)
          if(data.status==200){
            this.$message.success('添加成功!')
            this.dialogFormVisible = false
            this.getList()
          }
          else this.$message.error('添加失败!')
        }
      })
    },
    addSubject() {
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.addForm.resetFields()
      })
    },
    editSubject(row) {
      this.editIndexDialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.editForm.resetFields()
        this.editForm.subjectName = row.subjectName
        this.editForm.isFrontDisplay = row.isFrontDisplay
      })
    },
    clearSearch() {
      this.subjectsParams.subjectName = ''
    },
    async getList() {
      const { data: res } = await list(this.subjectsParams)
      this.tableData = res.items
      this.total = res.counts
    },
    changePager(newPage) {
      this.subjectsParams.page = newPage
      this.getList()
    },
    objectsSearch() {
      this.getList()
      this.subjectsParams.subjectName = ''
    }
  },
  mounted() {
    this.getList()
  }
}
</script>
<style scoped>
.inputHead {
  margin: 10px 0;
}
.inputHead .el-button {
  float: right;
  margin-left: 10px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
