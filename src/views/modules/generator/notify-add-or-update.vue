<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="消息内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="消息内容"></el-input>
    </el-form-item>
    <el-form-item label="消息类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="消息类型"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="messageStatus">
      <el-input v-model="dataForm.messageStatus" placeholder="状态"></el-input>
    </el-form-item>
    <el-form-item label="关联ID" prop="linkId">
      <el-input v-model="dataForm.linkId" placeholder="关联ID"></el-input>
    </el-form-item>
    <el-form-item label="通知人" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="通知人"></el-input>
    </el-form-item>
    <el-form-item label="创建人" prop="creator">
      <el-input v-model="dataForm.creator" placeholder="创建人"></el-input>
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
          content: '',
          type: '',
          createAt: '',
          messageStatus: '',
          linkId: '',
          userId: '',
          creator: ''
        },
        dataRule: {
          content: [
            { required: true, message: '消息内容不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '消息类型不能为空', trigger: 'blur' }
          ],
          messageStatus: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ],
          linkId: [
            { required: true, message: '关联ID不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '通知人不能为空', trigger: 'blur' }
          ],
          creator: [
            { required: true, message: '创建人不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/notify/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.content = data.notify.content
                this.dataForm.type = data.notify.type
                this.dataForm.messageStatus = data.notify.messageStatus
                this.dataForm.linkId = data.notify.linkId
                this.dataForm.userId = data.notify.userId
                this.dataForm.creator = data.notify.creator
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
              url: this.$http.adornUrl(`/generator/notify/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'content': this.dataForm.content,
                'type': this.dataForm.type,
                'messageStatus': this.dataForm.messageStatus,
                'linkId': this.dataForm.linkId,
                'userId': this.dataForm.userId,
                'creator': this.dataForm.creator
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
      }
    }
  }
</script>
