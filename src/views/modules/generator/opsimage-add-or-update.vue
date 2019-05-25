<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
    <el-form-item label="运维记录ID" prop="recordId">
      <el-input v-model="dataForm.recordId" placeholder="运维记录ID"></el-input>
    </el-form-item>
    <el-form-item label="运维记录表" prop="filePath">
      <el-input v-model="dataForm.filePath" placeholder="运维记录表"></el-input>
    </el-form-item>
    <el-form-item label="上传人" prop="uploadUser">
      <el-select v-model="dataForm.uploadUser" placeholder="上传人">
        <el-option v-for="(item,index) in userList" :key="index" :label="item.username" :value="item.userId"></el-option>
      </el-select>
      <!-- <el-input v-model="dataForm.uploadUser" placeholder="上传人"></el-input> -->
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          recordId: '',
          filePath: '',
          createAt: '',
          uploadUser: ''
        },
        dataRule: {
          recordId: [
            { required: true, message: '运维记录ID不能为空', trigger: 'blur' }
          ],
          filePath: [
            { required: true, message: '运维记录表不能为空', trigger: 'blur' }
          ],
          uploadUser: [
            { required: true, message: '上传人不能为空', trigger: 'blur' }
          ]
        },

        userList: []
      }
    },
    mounted () {
      this.getUserList()
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/opsimage/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.recordId = data.opsImage.recordId
                this.dataForm.filePath = data.opsImage.filePath
                this.dataForm.createAt = data.opsImage.createAt
                this.dataForm.uploadUser = data.opsImage.uploadUser
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/opsimage/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'recordId': this.dataForm.recordId,
                'filePath': this.dataForm.filePath,
                'createAt': this.dataForm.createAt,
                'uploadUser': this.dataForm.uploadUser
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      getUserList () {
        this.$http({
            url : this.$http.adornUrl('/sys/user/list')
        }).then(({data}) => {
            if(data.page) {
                this.userList = data.page.list || []
            }
        })
      }
    }
  }
</script>
