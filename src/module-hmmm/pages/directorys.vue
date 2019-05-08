<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>学科管理</el-breadcrumb-item>
        <el-breadcrumb-item>{{subjectName}}</el-breadcrumb-item>
        <el-breadcrumb-item>目录管理</el-breadcrumb-item>
      </el-breadcrumb>
      <el-button plain @click="addIndex()" class="btn">新建目录</el-button>
      <el-button plain class="btn" @click="lastSubjects()">返回学科</el-button>
      <el-row :gutter="20" class="inputHead">
        <el-col :span="3">
          <el-input placeholder="请输入目录名称" :span="3" v-model="indexsParams.directoryName"></el-input>
        </el-col>
        <el-col :span="3">
          <el-dropdown trigger="click" @command="handleCommand">
            <el-input placeholder="请选择状态" :span="3" v-model="nowstate"></el-input>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item icon="el-icon-plus" command="1">开启</el-dropdown-item>
              <el-dropdown-item icon="el-icon-circle-plus" command="0">屏蔽</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </el-col>
        <el-col :span="12">
          <el-button @click="clearSearch()">清除</el-button>
          <el-button type="success" @click="objectsSearch()">搜索</el-button>
        </el-col>
      </el-row>
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column prop="id" label="序号"></el-table-column>
        <el-table-column prop="subjectName" label="学科名称"></el-table-column>
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
            <el-button
              :disabled="!!scope.row.state"
              @click="editIndex(scope.row)"
              type="primary"
              plain
            >修改</el-button>
            <el-button
              v-if="!scope.row.state"
              type="primary"
              plain
              @click="changeState(scope.row)"
            >启用</el-button>
            <el-button v-else type="primary" plain @click="changeState(scope.row)">禁用</el-button>
            <el-button
              :disabled="!!scope.row.state"
              type="primary"
              plain
              @click="delIndex(scope.row.id)"
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        background
        layout="prev, pager, next"
        @current-change="changePager"
        :page-size="indexsParams.pagesize"
        :current-page="indexsParams.page"
        :total="total"
      ></el-pagination>
      <el-dialog width="400px" title="新建目录" :visible.sync="addIndexDialogFormVisible">
        <el-form
          ref="addForm"
          :model="addForm"
          :rules="addRules"
          label-width="80px"
          autocomplete="off"
        >
          <el-form-item label="目录名称" prop="directoryName">
            <el-input v-model="addForm.directoryName"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="addIndexDialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="addSubmit()">确 定</el-button>
        </div>
      </el-dialog>
      <el-dialog width="400px" title="修改目录" :visible.sync="editIndexDialogFormVisible">
        <el-form
          ref="editForm"
          :model="editForm"
          :rules="editRules"
          label-width="80px"
          autocomplete="off"
        >
          <el-form-item label="目录名称" prop="directoryName">
            <el-input v-model="editForm.directoryName"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="editIndexDialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="editSubmit()">确 定</el-button>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import {
  list,
  simple,
  detail,
  add,
  update,
  remove,
  state
} from '@/api/hmmm/directorys'
export default {
  name: 'DirectorysList',
  data() {
    return {
      nowstate: '',
      subjectName: this.$route.query.subjectName,
      editIndexDialogFormVisible: false,
      editForm: {
        id: 0,
        subjectID: this.$route.query.subjectID,
        directoryName: ''
      },
      editRules: {
        directoryName: [
          { required: true, message: '目录名必填!', trigger: 'blur' }
        ]
      },
      addIndexDialogFormVisible: false,
      addForm: {
        subjectID: this.$route.query.subjectID,
        directoryName: ''
      },
      addRules: {
        directoryName: [
          { required: true, message: '目录名必填!', trigger: 'blur' }
        ]
      },
      tableData: [],
      total: 0,
      indexsParams: {
        page: 1,
        pagesize: 10,
        directoryName: '',
        state: 0,
        subjectID: this.$route.query.subjectID
      }
    }
  },
  methods: {
    handleCommand(command) {
      if (command == 1) {
        this.nowstate = '开启'
        this.indexsParams.state = 1
      }
      if (command == 0) {
        this.nowstate = '屏蔽'
        this.indexsParams.state = 0
      }
    },
    clearSearch() {
      this.indexsParams.directoryName = ''
      this.nowstate = ''
    },
    objectsSearch() {
      this.getList()
      this.indexsParams.directoryName = ''
    },
    async changeState(row) {
      const rowState = !row.state
      const data = await state(row.id, rowState)
      if (data.status == 200) {
        this.$message.success('修改状态成功')
        this.getList()
      } else this.$message.error('修改状态失败')
    },
    delIndex(id) {
      this.$confirm('此操作将永久删除目录 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await remove(id)
            .then(response => {
              this.$message.success('成功删除了目录' + '!')
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
    lastSubjects() {
      this.$router.push({ path: '/subjects/list' })
    },
    editIndex(row) {
      this.editIndexDialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.editForm.resetFields()
        this.editForm.directoryName = row.directoryName
      })
    },
    addIndex() {
      this.addIndexDialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.addForm.resetFields()
      })
    },
    async addSubmit() {
      // 数据有问题 等待修正
      this.$refs.addForm.validate(async valid => {
        if (valid) {
          const data = await add(this.addForm)
          if (data.status == 200) {
            this.$message.success('添加成功!')
            this.addIndexDialogFormVisible = false
            this.getList()
          } else this.$message.error('添加失败!')
        }
      })
    },
    async editSubmit() {
      // 数据有问题 等待修正
      this.$refs.editForm.validate(async valid => {
        if (valid) {
          const data = await update(this.editForm)
          if (data.status == 200) {
            this.$message.success('修改成功!')
            this.editIndexDialogFormVisible = false
            this.getList()
          } else this.$message.error('修改失败!')
        }
      })
    },
    async getList() {
      const { data: res } = await list(this.indexsParams)
      this.tableData = res.items
      this.total = res.counts
    },
    changePager(newPage) {
      this.indexsParams.page = newPage
      this.getList()
    }
  },
  mounted() {
    this.getList()
  }
}
</script>

<style scoped>
.el-dropdown-item {
  width: 100%;
}
.btn {
  margin-top: 20px;
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
