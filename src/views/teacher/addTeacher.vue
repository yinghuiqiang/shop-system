<template>
  <div>
    <el-breadcrumb separator="/">
      <el-breadcrumb-item>企业门户管理系统</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/' }">
        <span class="breadcrumbTitle">添加教师</span>
      </el-breadcrumb-item>
    </el-breadcrumb>

    <el-form
      :model="addTeacherForm"
      :rules="addTeacherRules"
      ref="ruleForm"
      label-width="80px"
      class="demo-ruleForm"
    >
      <el-form-item label="教师姓名" prop="teacherName">
        <el-input v-model="addTeacherForm.teacherName"></el-input>
      </el-form-item>
      <el-form-item label="年龄" prop="age">
        <el-select
          style="width: 100%"
          v-model="addTeacherForm.age"
          placeholder="请选择年龄"
        >
          <el-option
            v-for="(item, index) in optionsAge"
            :key="index"
            :label="item"
            :value="item"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="性别" prop="gender">
        <el-select
          style="width: 100%"
          v-model="addTeacherForm.gender"
          placeholder="请选择性别"
        >
          <el-option
            v-for="item in optionsGender"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="个人简介" prop="">
        <el-input
          type="textarea"
          v-model="addTeacherForm.introduction"
          row="6"
        ></el-input>
      </el-form-item>
      <el-form-item label="添加课程" prop="classCode">
        <el-select
          v-model="addTeacherForm.classCode"
          multiple
          collapse-tags
          collapse-tags-tooltip
          :max-collapse-tags="3"
          placeholder="请添加课程"
          style="width: 100%"
        >
          <el-option
            v-for="item in classList"
            :key="item.classId"
            :label="item.className"
            :value="item.classId"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="教师头像" prop="avatar">
        <upload
          :avatar="addTeacherForm.avatar"
          @avatarChange="uploadHandler"
        ></upload>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm">添加教师</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
import upload from '@/components/upload/upload.vue'
import { ElMessage } from 'element-plus'
import uploadFn from '@/util/upload'
import { getCourseList,addTeacher } from '@/api'

const ruleForm = ref()

const addTeacherForm = reactive({
  teacherName: '',
  age: null,
  gender: 1,
  introduction: '',
  classCode: [],
  avatar: '',
  file: null,
})

const addTeacherRules = reactive({
  teacherName: [
    { required: true, message: '请输入教师姓名', trigger: 'blur' },
    {
      min: 2,
      max: 20,
      message: '长度在 2 到 20 个字符',
      trigger: 'blur',
    },
  ],
  age: [
    { required: true, message: '请输入教师年龄', trigger: 'change' },
  ],
  gender: [{ required: true, message: '请选择性别', trigger: 'change' }],
  classCode: [{ required: true, message: '请添加课程', trigger: 'blur' }],
  avatar: [{ required: true, message: '请添加头像', trigger: 'change' }],
})

const submitForm = () => {
  ruleForm.value.validate(async (valid) => {
    if (valid) {
      console.log(addTeacherForm);
      const result = await addTeacher(addTeacherForm)
      if ((result.message = 'success')) {
        ElMessage.success({
          message: '添加教师成功',
          type: 'success',
        })
        addTeacherForm.teacherName = ''
        addTeacherForm.age = 20
        addTeacherForm.gender = 1
        addTeacherForm.introduction = ''
        addTeacherForm.classCode = []
        addTeacherForm.avatar = ''
        addTeacherForm.file = null
      }
    }
  })
}
// 性别
const optionsGender = [
  {
    label: '男',
    value: 1,
  },
  {
    label: '女',
    value: 2,
  },
]
const optionsAge = []
for (let i = 20; i <= 45; i++) {
  optionsAge.push(i)
}
const uploadHandler = (file) => {
  // console.log(file);
  addTeacherForm.avatar = URL.createObjectURL(file)
  addTeacherForm.file = file
}
const classList = ref([])
onMounted(async () => {
  const result = await getCourseList()
  if (result.length >= 0) {
    result.forEach((item) => {
      let classItem = {
        classId: item._id,
        className: item.className,
      }
      classList.value.push(classItem)
    })
  }
})
</script>

<style scope lang="scss">
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}

.demo-ruleForm {
  margin-top: 50px;
}
</style>
