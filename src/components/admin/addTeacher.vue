<!-- 添加教师 -->
<template>
  <section class="add">
    <el-form ref="formRef" :model="form" :rules="addFormRules" label-width="80px">
      <el-form-item label="姓名" prop="teacherName">
        <el-input v-model="form.teacherName"></el-input>
      </el-form-item>
      <el-form-item label="学院" prop="institute">
        <!-- <el-input v-model="form.institute"></el-input> -->
        <el-select v-model="form.institute" placeholder="请选择学院" style="width: 100%">
          <el-option
            v-for="item in instituteOps"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
        <!-- <el-input v-model="form.sex"></el-input> -->
        <el-select v-model="form.sex" placeholder="请选择性别" style="width: 100%">
          <el-option
            v-for="item in sexOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="电话号码" prop="tel">
        <el-input v-model="form.tel"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="pwd">
        <el-input v-model="form.pwd"></el-input>
      </el-form-item>
      <el-form-item label="身份证号" prop="cardId">
        <el-input v-model="form.cardId"></el-input>
      </el-form-item>
      <el-form-item label="职称" prop="type">
        <!-- <el-input v-model="form.type"></el-input> -->
        <el-select v-model="form.type" placeholder="请选择职称" style="width: 100%">
          <el-option
            v-for="item in typeOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit()">立即创建</el-button>
        <el-button type="text" @click="cancel()">取消</el-button>
      </el-form-item>

      <!-- 导入文件 -->
      <el-form-item>
        <el-button type="primary" @click="uploadFile">导入</el-button>
      </el-form-item>
    </el-form>
    <!-- 上传文件对话框 -->
    <el-dialog
      title="上传文件"
      width="30%"
      :visible.sync="uploadDialogVisible"
    >
      <el-row>
        <el-col :span="22">
            <el-upload class="upload"
                       ref="upload"
                       action=""
                       :limit="limitNum"
                       :on-exceed="handleExceed"
                       :before-upload="beforeUpload"
                       :file-list="fileList"
                       :auto-upload="false">
                <el-button class="upload_btn" slot="trigger" size="medium" type="primary">选取文件</el-button>
                <div slot="tip" class="el-upload_tip">只能上传.xls文件,且不超过1M</div>
            </el-upload>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitUpload ">确定上传</el-button>
        <el-button @click="uploadDialogVisible = false">取 消</el-button>
      </span>
    </el-dialog>
  </section>
</template>

<script>
export default {
  data() {
    // 邮箱的的规则
    var checkEmail = (rule, value, cb) => {
      // 验证邮箱的正则表达式
      const regEmail = /^\w+@\w+(\.\w+)+$/
      if (regEmail.test(value)) {
        // 合法的邮箱
        return cb()
      }
      cb(new Error('请输入合法的邮箱'))
    }
    // 验证手机号的规则
    var checkMobile = (rule, value, cb) => {
      // 验证手机号的正则表达式
      const regMobile = /^1[34578]\d{9}$/
      if (regMobile.test(value)) {
        return cb()
      }
      cb(new Error('请输入合法的手机号'))
    }
    // 验证身份证号码
    var checkCardId = (rule, value, cb) => {
      // 验证身份证号码的正则表达式
      const regCardId = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/
      if(regCardId.test(value)) {
        return cb()
      }
      cb(new Error('请输入合法的身份证号'))
    }

    return {
      form: { //表单数据初始化
        teacherName: null,
        // studentName: null,
        grade: null,
        major: null,
        clazz: null,
        institute: null,
        tel: null,
        email: null,
        pwd: null,
        cardId: null,
        sex: null,
        type: null,
        role: 2
      },

      // 性别选择
      sexOptions: [{
        value: '男',
        label: '男'
      }, {
        value: '女',
        label: '女'
      }],
      // 职称选择
      typeOptions: [{
        value: '助教',
        label: '助教'
      }, {
        value: '讲师',
        label: '讲师'
      }, {
        value: '副教授',
        label: '副教授'
      }, {
        value: '教授',
        label: '教授'
      }],
      // 学院选择
      instituteOps: [{
        value: '信息学院',
        label: '信息学院'
      }, {
        value: '数理学院',
        label: '数理学院'
      }, {
        value: '电气学院',
        label: '电气学院'
      }, {
        value: '人文学院',
        label: '人文学院'
      }, {
        value: '环境学院',
        label: '环境学院'
      }, {
        value: '机械学院',
        label: '机械学院'
      }, {
        value: '管理学院',
        label: '管理学院'
      }, {
        value: '交通学院',
        label: '交通学院'
      }],

      // 添加教师的表单验证规则对象
      addFormRules: {
        // 姓名验证
        teacherName: [
          { required: true, message: '请输入学生姓名', trigger: 'blur' },
          {
            min: 2,
            max: 16,
            message: '用户名长度长度在 3 到 16个字符',
            trigger: 'blur'
          }
        ],
        // 电话号码验证
        tel:[
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ],
        // 邮箱验证
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        // 身份证号码验证
        cardId: [
          { required: true, message: '请输入身份证号', trigger: 'blur' },
          { validator: checkCardId, trigger: 'blur' }
        ],
        // 密码验证
        pwd: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 6,
            max: 16,
            message: '用户名长度长度在 6 到 16 个字符',
            trigger: 'blur'
          }
        ]
      },

      // 控制对话框是否可见 
      uploadDialogVisible: false,
      uploadLoading: false,
      downloadLoading: '',
      // 可以接受的文件类型
      acceptFileType: '.xls, .xlsx, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel',
      // 上传文件的url地址
      uploadURL: 'http://101.132.187.253:9000/api/batchAddTeacher',
      limitNum: 1,
      // 文件列表
      fileList: []
    };
  },
  methods: {
    onSubmit() { //数据提交
      this.$refs.formRef.validate( valid => {
        if (!valid) return
        this.$axios({
          url: '/api/teacher',
          method: 'post',
          data: {
            ...this.form
          }
        }).then(res => {
          if(res.data.code == 200) {
          this.$message({
            message: '数据添加成功',
            type: 'success'
          })
          this.$router.push({path: '/teacherManage'})
        }
      })
      })
      
    },
    cancel() { //取消按钮
      this.form = {}
    },

    // 导入文件按钮
    uploadFile() {
      var that = this
      this.uploadLoading = false
      this.uploadDialogVisible = true
      this.fileList = []
      setTimeout(() => {
        that.$refs.upload.clearFiles()
      }, 100);
    },
    // 上传文件前
    async beforeUpload(file) {
      var that = this
      // 判断文件类型
      // var fileName = file.name.substring(file.name.lastIndexOf('.') + 1)
      // if(fileName != ('.xlsx' || '.xls' || 'application/vnd.ms-excel' || 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet')) {
      //   that.$message({
      //     type: 'error',
      //     showClose: true,
      //     duration: 3000,
      //     message: '文件类型不是excel'
      //   })
      //   that.uploadDialogVisible = false
      //   return false
      // }

      // 读取文件大小
      var fileSize = file.size
      if(fileSize > this.limitNum*1024*1024) {
        that.$message({
          type: 'error',
          showClose: true,
          duration: 3000,
          message: '文件大小超过1M'
        })
        that.uploadDialogVisible = false
      }

      that.downloadLoading = that.$loading({
        lock:true,
        text:'数据导入中...',
        spinner:'el-icon-loading',
        background:'rgba(0,0,0,0.7)'
      })

      let formData = new FormData()
      formData.append('file', file);
      console.log('axios');
      this.$axios({
        method: 'post',
        url: this.uploadURL,
        data: formData,
        // headers: {
        //   "Content-Type": ""
        // }
      }).then( res => {
        that.downloadLoading.close()
        that.uploadLoading = false
        // console.log(res);
        if(res.status === 200) {
          that.uploadDialogVisible = false
          that.$message.success('上传成功')
        } else {
          that.uploadDialogVisible = false
          that.$mesaage.error('上传失败')
        }
      })
    },

    // 文件超过个数
    handleExceed(files, fileList) {
      this.$message.warning('只能选择1个文件')
    },
    submitUpload() {
      var that = this
      this.uploadLoading = true
      setTimeout(() => {
        if(that.$refs.upload.$children[0].fileList.length == 1) {
          that.$refs.upload.submit()
        } else {
          that.uploadLoading = false
          that.$message({
            type: 'error',
            showClose: true,
            duration: 3000,
            message: '请选择文件'
          })
        }
      }, 100);
    },
  }
};
</script>
<style lang="scss" scoped>
.add {
  padding: 0px 40px;
  width: 400px;
}
.upload_btn {
  margin-bottom: 10px;
}
</style>

