<template>
    <div>
        <el-card>
            <el-form>
                <el-form-item label="标题">
                    <el-input placeholder="请输入文章标题" style="width:60%" v-model="reqParams.title"></el-input>
                </el-form-item>
                <quill-editor v-model="text"></quill-editor>
                <el-form-item label="视频地址:" class="videoInitial">
                    <el-input placeholder="视频地址" style="width:60%" v-model="video"></el-input>
                </el-form-item>
                <div class="butConfirm">
                    <el-button>取消</el-button>
                    <el-button type="primary" @click="addSkill()">提交</el-button>
                </div>
            </el-form>
        </el-card>
    </div>
</template>

<script>
import VueQuillEditor from 'vue-quill-editor'
import { add } from '@/api/hmmm/articles.js'
export default {
    data() {
        return {
            // 所需参数
            reqParams: {
                // 标题内容
                title: '',
                // 富文本框
                articleBody: '',
                // 视频地址
                videoURL: ''
            }
        }
    },
    methods: {
        // addSkill() {
        //     const res = await add()
        // }
    }
}
</script>

<style scoped>
.butConfirm {
    display: flex;
    justify-content: center;
    width: 100%;
    margin: 20px 0;
    padding: 0 auto;
}
.butConfirm .el-button {
    margin-left: 60px;
}
.videoInitial {
    margin: 20px 0;
}
</style>
