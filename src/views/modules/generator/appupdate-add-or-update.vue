<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="应用名称" prop="appName">
        <el-input v-model="dataForm.appName" placeholder="应用名称"></el-input>
      </el-form-item>
      <el-form-item label="版本" prop="version">
        <el-input v-model="dataForm.version" placeholder="版本"></el-input>
      </el-form-item>

      <el-form-item label="文件" prop="path">
        <el-upload
          v-bind:class="{hideUpload: (fileList.length > 0)}"
          :file-list="fileList"
          action=""
          list-type="text"
          :before-upload="uploadFunc"
          :on-remove="handleRemove">
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>


      <!-- <el-form-item label="文件名称" prop="fileName">
        <el-input v-model="dataForm.fileName" placeholder="文件名称"></el-input>
      </el-form-item> -->
    </el-form>

    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data() {
      return {
        uploadUrl: this.$http.adornUrl(`/generator/appupdate/upload`),
        fileList: [],
        visible: false,
        dataForm: {
          id: 0,
          appName: '',
          version: '',
          createAt: '',
          path: '',
          fileName: ''
        },
        dataRule: {
          appName: [
            {required: true, message: '应用名称不能为空', trigger: 'blur'}
          ],
          version: [
            {required: true, message: '版本不能为空', trigger: 'blur'}
          ],
          path: [
            {required: true, message: '文件路径不能为空', trigger: 'blur'}
          ],
          // fileName: [
          //   {required: true, message: '文件名称不能为空', trigger: 'blur'}
          // ]
        }
      }
    },
    watch: {
      // visible : (val) => {
      // console.log(this.fileList)
      //     this.fileList = []
      
      // }
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.fileList = []
        this.visible = true
        var that = this
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/appupdate/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.appName = data.appUpdate.appName
                this.dataForm.version = data.appUpdate.version
                this.dataForm.createAt = data.appUpdate.createAt
                this.dataForm.path = data.appUpdate.path
                this.dataForm.fileName = data.appUpdate.fileName
                this.fileList = [{
                  id:1,
                  name: data.appUpdate.fileName,
                  url: data.appUpdate.path
                }]
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/appupdate/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'appName': this.dataForm.appName,
                'version': this.dataForm.version,
                'createAt': this.dataForm.createAt,
                'path': this.dataForm.path,
                'fileName': this.dataForm.fileName
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

      handleRemove(file, fileList) {
        this.dataForm.path = '';
        this.dataForm.fileName = '';
        this.fileList = fileList
      },
      uploadFunc(file) {
        var data = new FormData()
        data.append('file', file)
        this.$http({
          url: this.$http.adornUrl(`/generator/appupdate/upload`),
          method: 'post',
          data: data,
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        }).then(({data}) => {
          this.dataForm.path = data.path
          this.fileList.push({
            id: 1,
            url: data.path,
            name: file.name
          })
        })

        return false;
      }
    }
  }
</script>
