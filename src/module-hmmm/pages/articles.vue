<template>
  <div class="app-container">
      <el-card>
        <el-button type="info" plain @click="addInterview()">新增面试技巧</el-button>
        <!-- 搜索 -->
        <el-row :gutter="20" class="search">
          <el-col :span="10">
            <el-input placeholder="请输入题目编号/题干" v-model="reqParams.keyword">
              <template slot="prepend">关键字</template>
            </el-input>
          </el-col>
          <el-col :span="4" class="delSearch">
            <el-button @click="delSearchMod()">清除</el-button>
            <el-button type="primary" @click="searchClick()">搜索</el-button>
          </el-col>
        </el-row>
        <!-- 表格 -->
        <el-table :data="getList" style="width: 100%">
          <el-table-column label="序号">
            <template slot-scope="scope">
              <span size="medium">{{ scope.row.id }}</span>
            </template>
          </el-table-column>
          <el-table-column label="标题">
            <template slot-scope="scope">
              <span size="medium">{{ scope.row.title }}</span>
            </template>
          </el-table-column>
          <el-table-column label="阅读数">
            <template slot-scope="scope">
              <span size="medium">{{ scope.row.visits }}</span>
            </template>
          </el-table-column>
          <el-table-column label="状态">
            <template slot-scope="scope" >
              <span size="medium" v-if="scope.row.state === true">启用</span>
              <span size="medium" v-else>禁用</span>
            </template>
          </el-table-column>
          <el-table-column label="录入人">
            <template slot-scope="scope">
              <span size="medium">{{ scope.row.creator }}</span>
            </template>
          </el-table-column>
          <el-table-column label="操作"  width="300">
            <template slot-scope="scope">
              <el-button size="mini" type="primary" @click="showCenterDialogVisible(scope.row.id)">浏览</el-button>
              <el-button size="mini" type="success" @click="showDialogVisible(scope.row.id)">修改</el-button>
              <el-button size="mini" type="danger" @click="delRow(scope.row.id)">删除</el-button>
              <template>
                <el-button size="mini" type="warning" @click="changeStart()" v-if="scope.row.state === true">禁用</el-button>
                <el-button size="mini" type="warning" @click="changeStart()" v-else>启用</el-button>                
              </template>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-pagination background @current-change='pageChange' :total="1000">
        </el-pagination>
      </el-card>
      <!-- 修改对话框dialog -->
      <el-dialog
        title="面试技巧文章"
        :visible.sync="dialogVisible"
        width="30%">
        <el-form :model="form" ref="form" :rules="editFormRules">
          <el-form-item label="标题" prop="titleInput">
            <el-input v-model="form.titleInput" class="titleInput"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="editSubmit()">确 定</el-button>
        </span>
      </el-dialog>
      <!-- 浏览对话框 -->
      <el-dialog
        title="文章内容浏览"
        :visible.sync="centerDialogVisible"
        width="50%"
        center>
        <span>{{textContent}}</span>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="centerDialogVisible = false">关 闭</el-button>
        </span>
      </el-dialog>
  </div>
</template>

<script>
import { list, remove,update,detail } from '../../api/hmmm/articles.js'
export default {
  name: 'ArticlesList',
  data() {
    return {
      // keyword: '',
      // 数据列表
      getList: [],
      // 获取列表参数
      reqParams: {
        // 当前页数
        page: 1,
        keyword: ''
      },
      // 总页数
      pages: 1,
      // 启用禁用值
      state: true,
      // 显示修改对话框
      dialogVisible: false,
      // 标题修改表单数据
      form: {
        titleInput: ''
      },
      // 修改验证
      editFormRules: {
        titleInput: [
          {required: true ,message:'标题不能为空',trigger:'blur'}
        ]
      },
      // 显示当前行的id
      rowId: '',
      // 浏览对话框dialog
      centerDialogVisible: false,
      // 文章内容
      textContent: ''
    }
  },
  mounted () {
    this.getData()
  },
  methods: {
    // 新增面试技巧
    addInterview() {
      this.$router.push('/articles/addlist')
    },
    // 清除按钮
    delSearchMod() {
      this.input1 = ''
    },
    // 获取列表内容
    async getData() {
      const res = await list(this.reqParams)
      // console.log(res)
      this.getList = res.data.items
      this.state = res.data.state
      console.log()
    },
    // 分页组件改变时
    pageChange(newPage) {
      this.getData()
      this.reqParams.page = newPage
      // console.log(this.reqParams.page)
    },
    // 删除行
    delRow(id) {
      this.$confirm('此操作将永久删除学科 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
      .then(async () => {
        await remove(id)
          .then(response => {
            this.$message.success('成功删除了学科' + '!')
            this.getData()
          })
          .catch(response => {
            this.$message.error('删除失败!')
          })
      })
      .catch(() => {
        this.$message.info('已取消操作!')
      })
    },
    // 更改启用禁用
    changeStart () {
      this.$confirm('此操作将更改当前状态' + '是否继续', '提示', {
        type: 'warning'
      })
      .then(response => {
        this.$message.success('更改状态成功')
        this.state = !this.state
        // console.log(this.state)
        this.getData()
      })
      .catch(() => {
        this.$message.error('更改状态失败')
      })
    },
    // 显示修改对话框
    showDialogVisible(id) {
      // console.log(id)
      this.dialogVisible = true
      this.$nextTick(async () => {
        this.$refs.form.resetFields()
        const res = await detail(id)
        // console.log(res)
        this.form.titleInput = res.data.title
        this.rowId = res.data.id
      })
    },
    // 提交修改对话框的内容
    editSubmit() {
      this.$refs.form.validate(async valid => {
        if (valid) {
          const id = this.rowId
          // console.log(id)
          const res = await update(id)
          // console.log(res)
          if (res.status !== 200) return this.$message.error('数据修改失败')
          this.$message.success('数据修改成功')
          this.dialogVisible = false
        }
      })
    },
    // 显示浏览对话框
    async showCenterDialogVisible(id) {
      this.centerDialogVisible = true
      const {data:{articleBody}} = await detail(id)
      // console.log(articleBody)
      this.textContent = articleBody
    },
    // 搜索关键字 //存在小问题
    searchClick() {
      this.reqParams.page = 1
      // this.pageChange()
      this.getData()
    }
  }
}
</script>

<style scoped>
.search {
  margin: 10px 0;
}
.search .delSearch {
  float: right;
}
.el-pagination {
  float: right;
  margin: 10px 0;
}
.titleInput {
  margin-top: 20px; 
}
</style>
