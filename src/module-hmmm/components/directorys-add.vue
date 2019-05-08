<template>
  <div class="dashboard-container">
    <div class="app-container">
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
    </div>
  </div>
</template>

<script>
import { list, simple, detail, add, update, remove } from '@/api/hmmm/subjects'
export default {
  name: 'DirectorysAdd',
  data() {
    return {
       addForm: {
        subjectName: '',
        isFrontDisplay:true
      },
      addRules: {
        subjectName: [
          {required: true, message: '学科名必填!', trigger: 'blur'}
        ]
      },
      dialogFormVisible: false
    }
  },
  methods:{
    async addSubmit(){
      // 数据有问题 等待修正
      const {data:res} = await add(this.addForm)
      this.dialogFormVisible = false
      this.getList()
    },
    addSubject() {
      this.dialogFormVisible=true
    }
  }
}
</script>

<style scoped>
</style>
