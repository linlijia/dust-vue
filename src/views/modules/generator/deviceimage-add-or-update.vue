<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="设备mn" prop="mn">
      <el-input v-model="dataForm.mn" placeholder="设备mn"></el-input>
    </el-form-item>
    <el-form-item label="图片" prop="path">
      <el-upload
        v-bind:class="{hideUpload: (fileList.length > 0)}"
        :file-list="fileList"
        action=""
        list-type="picture-card"
        :before-upload="uploadFunc"
        :on-remove="handleRemove">
        <i class="el-icon-plus"></i>
      </el-upload>
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
        uploadUrl: this.$http.adornUrl('/generator/opsimage/upload'),
        fileList: [],
        visible: false,
        dataForm: {
          id: 0,
          mn: '',
          path: ''
        },
        dataRule: {
          mn: [
            { required: true, message: '设备mn不能为空', trigger: 'blur' }
          ],
          path: [
            { required: true, message: '图片不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/deviceimage/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.mn = data.deviceImage.mn
                this.dataForm.path = data.deviceImage.path
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
              url: this.$http.adornUrl(`/generator/deviceimage/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'mn': this.dataForm.mn,
                'path': this.dataForm.path
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
      handleRemove (file, fileList) {
        this.dataForm.path = '';
        this.fileList = []
      },
      uploadFunc (file) {
        var data = new FormData()
        data.append('file', file)
        this.$http({
          url: this.$http.adornUrl(`/generator/opsimage/upload`),
          method: 'post',
          data: data,
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        }).then(({data}) => {
          console.log(data)
          this.dataForm.path = data.data.filePath
          this.fileList.push({
            id:1,
            url: data.filePath
          })
        })

        return false;
      }
    }
  }
</script>
