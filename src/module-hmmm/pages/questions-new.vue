<template>
  <!--试题录入-->
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <el-form
          :model="formlist"
          :rules="rules"
          ref="formlist"
          label-width="60px"
          class="demo-ruleForm"
          autocomplete="off"
        >
          <el-form-item label="学科:" prop="subject">
            <el-select v-model="formlist.subject" placeholder="请选择" class="width">
              <el-option
                v-for="(item,i) in list"
                :key="i"
                :label="item.subject"
                :value="item.subject"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="目录:" prop="catalog">
            <el-select v-model="formlist.catalog" placeholder="请选择" class="width">
              <el-option
                v-for="(item,i) in list"
                :key="i"
                :label="item.catalog"
                :value="item.catalog"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业:" prop="enterprise">
            <el-select v-model="formlist.enterprise" placeholder="请选择" class="width">
              <el-option
                v-for="(item,i) in list"
                :key="i"
                :label="item.enterprise"
                :value="item.enterprise"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="城市:" prop="province">
            <el-select
              class="filter-item"
              v-model="formlist.province"
              @change="handleProvince"
              filterable
            >
              <el-option
                v-for="item in citySelect.province"
                :key="item"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
            <el-select
              class="filter-item"
              style="margin-left: 10px;"
              v-model="formlist.city"
              @keyup.enter="handleFilter"
              filterable
            >
              <el-option
                v-for="item in citySelect.cityDate"
                :key="item"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向:" prop="direction">
            <el-select v-model="formlist.direction" placeholder="请选择" class="width">
              <el-option
                v-for="(item,i) in list"
                :key="i"
                :label="item.direction"
                :value="item.direction"
              ></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="题型:" prop="questionType">
            <el-radio-group v-model="formlist.questionType">
              <el-radio label="单选"></el-radio>
              <el-radio label="多选"></el-radio>
              <el-radio label="简答"></el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="难度:" prop="difficulty">
            <el-radio-group v-model="formlist.difficulty">
              <el-radio label="简单"></el-radio>
              <el-radio label="一般"></el-radio>
              <el-radio label="困难"></el-radio>
            </el-radio-group>
          </el-form-item>
          <!--富文本-->
          <el-form-item label="题干:" prop="question">
            <p>注:点击下方的按钮,选择语言插入后,代码会高亮显示</p>
            <quill-editor v-model="formlist.question"></quill-editor>
          </el-form-item>
          <el-form-item label="选项(以下选中的为正确答案):" prop="options" label-width="210px">
            <el-radio-group  v-model="formlist.options">
              <el-row class="center">
                <el-col :span="4" class="center">
                  <el-radio label="A:"></el-radio>
                </el-col>
                <el-col :span="12" class="center">
                  <el-input v-model="formlist.options.code"></el-input>
                </el-col>
                <el-col :span="6" class="center">
                  <!--图片上传-->
                  <el-upload
                    class="avatar-uploader"
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :show-file-list="false"
                    :on-success="handleAvatarSuccess()"
                    :before-upload="beforeAvatarUpload()"
                  >
                    <img v-if="formlist.options.img" :src="formlist.options.img" class="avatar">
                    <i v-else class="avatar-uploader-icon">上传图片</i>
                  </el-upload>
                </el-col>
                <el-col :span="2" class="center">
                  <el-checkbox v-model="formlist.options.isRight"></el-checkbox>
                </el-col>
              </el-row>
              <el-row class="center">
                <el-col :span="4" class="center">
                  <el-radio label="B:"></el-radio>
                </el-col>
                <el-col :span="12" class="center">
                  <el-input v-model="formlist.options.code"></el-input>
                </el-col>
                <el-col :span="6" class="center">
                  <!--图片上传-->
                  <el-upload
                    class="avatar-uploader"
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :show-file-list="false"
                    :on-success="handleAvatarSuccess()"
                    :before-upload="beforeAvatarUpload()"
                  >
                    <img v-if="formlist.options.img" :src="formlist.options.img" class="avatar">
                    <i v-else class="avatar-uploader-icon">上传图片</i>
                  </el-upload>
                </el-col>
                <el-col :span="1" class="center">
                  <el-checkbox v-model="formlist.options.isRight"></el-checkbox>
                </el-col>
              </el-row>

              <el-row class="center">
                <el-col :span="4" class="center">
                  <el-radio label="C:"></el-radio>
                </el-col>
                <el-col :span="12" class="center">
                  <el-input v-model="formlist.options.code"></el-input>
                </el-col>
                <el-col :span="6" class="center">
                  <!--图片上传-->
                  <el-upload
                    class="avatar-uploader"
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :show-file-list="false"
                    :on-success="handleAvatarSuccess()"
                    :before-upload="beforeAvatarUpload()"
                  >
                    <img v-if="formlist.options.img" :src="formlist.options.img" class="avatar">
                    <i v-else class="avatar-uploader-icon">上传图片</i>
                  </el-upload>
                </el-col>
                <el-col :span="1" class="center">
                  <el-checkbox v-model="formlist.options.isRight"></el-checkbox>
                </el-col>
              </el-row>

              <el-row class="center">
                <el-col :span="4" class="center">
                  <el-radio label="D:"></el-radio>
                </el-col>
                <el-col :span="12" class="center">
                  <el-input v-model="formlist.options.code"></el-input>
                </el-col>
                <el-col :span="6" class="center">
                  <!--图片上传-->
                  <el-upload
                    class="avatar-uploader"
                    action="https://jsonplaceholder.typicode.com/posts/"
                    :show-file-list="false"
                    :on-success="handleAvatarSuccess()"
                    :before-upload="beforeAvatarUpload()"
                  >
                    <img v-if="formlist.options.img" :src="formlist.options.img" class="avatar">
                    <i v-else class="avatar-uploader-icon">上传图片</i>
                  </el-upload>
                </el-col>
                <el-col :span="1" class="center">
                  <el-checkbox v-model="formlist.options.isRight"></el-checkbox>
                </el-col>
              </el-row>
            </el-radio-group>

            <div>
              <el-input
                class="input-new-tag"
                v-if="inputVisible"
                v-model="formlist.inputValue"
                style="width:200px;background-color:#F3F3F3"
                ref="saveTagInput"
                size="small"
                @keyup.enter.native="handleInputConfirm"
                @blur="handleInputConfirm"
              ></el-input>
              <el-button
                v-else
                class="button-new-tag"
                style="width:200px;background-color:#F3F3F3"
                @click="showInput"
              >+ 增加选项与答案</el-button>
            </div>
          </el-form-item>
          <el-form-item label="解析视频:" prop="videoURL" label-width="100px">
            <el-input v-model="formlist.videoURL" style="width:300px;" placeholder="请输入视频地址"></el-input>
          </el-form-item>
          <el-form-item label="答案解析:" prop="answer">
            <quill-editor v-model="formlist.answer"></quill-editor>
          </el-form-item>
          <el-form-item label="题目备注:" prop="remarks">
            <el-input type="textarea" placeholder="请输入内容" v-model="formlist.remarks"></el-input>
          </el-form-item>
          <el-form-item label="试题标签:" prop="tags" label-width="100px">
            <el-input v-model="formlist.tags" style="width:300px;" placeholder="请输入内容"></el-input>
          </el-form-item>
        </el-form>
        <el-button type="primary" @click="addsubmit()">提交</el-button>
        <el-button @click="quite()">取消</el-button>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list, add } from '../../api/hmmm/questions'
import { provinces, citys } from '@/api/hmmm/citys.js'

// 学科列表
// import { subject } from '../../api/hmmm/subjects'
// import { simple } from '../../api/hmmm/directorys'
export default {
  name: 'QuestionsNew',
  data() {
    return {
      // 学科
      // subjects: [],
      // 目录
      // catalogs: [],
      // 企业
      // companys: [],
      list: [],
      formlist: {
        subject: '',
        catalog: '',
        enterprise: '',
        province: '',
        city: this.city,
        direction: '',
        questionType: '',
        difficulty: '',
        question: '',
        options: {
          img: '',
          code: '',
          title: '',
          isRight: false
        },
        inputValue: '',
        videoURL: '',
        answer: '',
        remarks: '',
        tags: ''
      },
      citySelect: {
        province: [],
        cityDate: []
      },
      imageUrl: '',
      inputVisible: false,
      // 表单验证规则
      rules: {
        subject: [{ required: true, message: '请选择学科', trigger: 'change' }],
        catalog: [{ required: true, message: '请选择目录', trigger: 'change' }],
        enterprise: [
          { required: true, message: '请选择企业', trigger: 'change' }
        ],
        province: [
          { required: true, message: '请选择城市', trigger: 'change' }
        ],
        direction: [
          { required: true, message: '请选择方向', trigger: 'change' }
        ],
        questionType: [
          { required: true, message: '请选择题型', trigger: 'change' }
        ],
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'change' }
        ],
        question: [{ required: true, message: '请填写题干', trigger: 'blur' }],
        options: [{ required: true, message: '请选择选项', trigger: 'change' }],
        videoURL: [
          { required: true, message: '请输入解析视频', trigger: 'blur' }
        ],
        answer: [
          { required: true, message: '请输入答案解析', trigger: 'blur' }
        ],
        remarks: [
          { required: true, message: '请输入题目备注', trigger: 'blur' }
        ],
        tags: [{ required: true, message: '请输入试题标签', trigger: 'blur' }]
      }
    }
  },
  methods: {
    // async getsubject() {
    //   // const { data } = await list()
    //   // console.log(data.items, status)
    //   const {data} = await simple()
    //   console.log(data)
    //   this.subjects = data
    // },
    // async getcatalog() {
    //   // const { data } = await list()
    //   // console.log(data.items, status)
    //   const {data} = await simple()
    //   console.log(data)
    //   this.catalogs = data
    // },
    async getdata() {
      // const { data } = await list()
      // console.log(data.items, status)
      const { data } = await list()
      this.list = data.items
      // console.log(data.items)
    },
    // 获取省
    getCityData: function() {
      this.citySelect.province = provinces()
    },
    // 选省获取到市
    handleProvince: function(e) {
      this.citySelect.cityDate = citys(e)
      this.formlist.city = this.citySelect.cityDate[0]
    },
    // 显示input输入框
    showInput() {
      this.inputVisible = true
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },

    handleInputConfirm() {
      let inputValue = this.inputValue
      if (inputValue) {
        // this.dynamicTags.push(inputValue)
      }
      this.inputVisible = false
      this.inputValue = ''
    },
    // 图片上传之前
    beforeAvatarUpload() {},
    handleAvatarSuccess(res, file) {
      // this.formlist.options.img = URL.createObjectURL(file.raw)
    },
    // 提交数据
    addsubmit() {
      this.$refs.formlist.validate(async valid => {
        if (valid) {
          const res = await add(this.formlist)
          // if(res.status !==200) return this.$message.error('添加失败')
          // this.$message.success('添加成功')
          console.log(this.formlist)
          this.$router.push('/questions/choice')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    // 取消
    quite () {
      this.$router.push('/questions/choice')
    }
  },
  // 创建完毕状态
  created() {
    this.getCityData()
  },
  mounted() {
    // this.getsubject(),
    // this.getcatalog(),
    // this.getcompany()
    this.getdata(), this.getCityData(), this.handleProvince()
  }
}
</script>

<style scoped>
.el-card {
  width: 100%;
  height: 100%;
  padding: 0;
}

.app-container {
  padding: 0;
}

.el-card .el-card__body {
  padding: 0;
}

.width {
  width: 456px;
}

.avatar-uploader {
  background: #f2f2f2;
  width: 60px;
  height: 54px;
  border: 1px dashed #666;
  border-radius: 6px;
  cursor: pointer;
  overflow: hidden;
  text-align: center;
  margin-left: 10px;
  margin-bottom: 10px;
}

.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}

.avatar-uploader-icon {
  font-size: 12px;
  color: #8c939d;
  width: 60px;
  height: 54px;
  line-height: 54px;
  text-align: center;
}

.avatar {
  width: 60px;
  height: 54px;
  display: block;
  text-align: center;
}

.center {
  line-height: 58px;
  align-items: center;
}

.button-new-tag {
  margin-left: 10px;
  height: 32px;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}
.input-new-tag {
  width: 90px;
  margin-left: 10px;
  vertical-align: bottom;
}
</style>
