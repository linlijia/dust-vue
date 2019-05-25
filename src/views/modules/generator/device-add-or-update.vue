<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="100px">
      <el-form-item label="站点" prop="siteId">
        <!-- <el-input v-model="dataForm.siteId" placeholder="站点ID"></el-input> -->
        <el-select v-model="dataForm.siteId" placeholder="站点">
          <el-option v-for="(item,index) in stieLists" :key="index" :label="item.site" :value="item.id"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="MN码" prop="mn">
        <el-input v-model="dataForm.mn" placeholder="MN码"></el-input>
      </el-form-item>
      <el-form-item label="接口根路径" prop="interfacePath">
        <el-input v-model="dataForm.interfacePath" placeholder="接口根路径"></el-input>
      </el-form-item>
      <el-form-item label="服务端信息" prop="serviceInfo">
        <el-input v-model="dataForm.serviceInfo" placeholder="服务端信息"></el-input>
      </el-form-item>
      <el-form-item label="服务端密码" prop="passwd">
        <el-input v-model="dataForm.passwd" placeholder="服务端密码"></el-input>
      </el-form-item>
      <el-form-item label="系统类型" prop="systemType">
        <el-input v-model="dataForm.systemType" placeholder="系统类型"></el-input>
      </el-form-item>
      <el-form-item label="串口名" prop="serialPortType">
        <el-input v-model="dataForm.serialPortType" placeholder="串口名"></el-input>
      </el-form-item>
      <el-form-item label="温度湿度限制" prop="temperatureHumidityLimit">
        <el-input v-model="dataForm.temperatureHumidityLimit" placeholder="温度湿度限制"></el-input>
      </el-form-item>
      <el-form-item label="称量间隔（小时）" prop="uploadInterval">
        <el-input v-model="dataForm.uploadInterval" placeholder="称量间隔（小时）"></el-input>
      </el-form-item>
      <el-form-item label="启动任务小时" prop="taskHour">
        <el-input v-model="dataForm.taskHour" placeholder="启动任务小时"></el-input>
      </el-form-item>
      <el-form-item label="启动任务分钟" prop="taskMinute">
        <el-input v-model="dataForm.taskMinute" placeholder="启动任务分钟"></el-input>
      </el-form-item>
      <!--<el-form-item label="添加时间" prop="createAt">-->
      <!--<el-input v-model="dataForm.createAt" placeholder="添加时间"></el-input>-->
      <!--</el-form-item>-->
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
        visible: false,
        stieLists: [],
        dataForm: {
          id: 0,
          siteId: '',
          mn: '',
          interfacePath: '',
          serviceInfo: '',
          passwd: '',
          systemType: '',
          serialPortType: '',
          temperatureHumidityLimit: '',
          uploadInterval: '',
          createAt: '',
          taskHour: '',
          taskMinute: 0
        },
        dataRule: {
          siteId: [
            {required: true, message: '站点名称不能为空', trigger: 'blur'}
          ],
          mn: [
            {required: true, message: 'MN码不能为空', trigger: 'blur'}
          ],
          interfacePath: [
            {required: true, message: '接口根路径不能为空', trigger: 'blur'}
          ],
          serviceInfo: [
            {required: true, message: '服务端信息不能为空', trigger: 'blur'}
          ],
          passwd: [
            {required: true, message: '服务端密码不能为空', trigger: 'blur'}
          ],
          systemType: [
            {required: true, message: '系统类型不能为空', trigger: 'blur'}
          ],
          serialPortType: [
            {required: true, message: '串口名不能为空', trigger: 'blur'}
          ],
          temperatureHumidityLimit: [
            {required: true, message: '温度湿度限制不能为空', trigger: 'blur'}
          ],
          uploadInterval: [
            {required: true, message: '称量间隔（小时）不能为空', trigger: 'blur'}
          ],
          taskHour: [
            {required: true, message: '启动任务小时不能为空', trigger: 'blur'}
          ],
          taskMinute: [
            {required: true, message: '启动任务分钟不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    mounted() {
      this.getSiteList()
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/device/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.siteId = data.device.siteId
                this.dataForm.mn = data.device.mn
                this.dataForm.interfacePath = data.device.interfacePath
                this.dataForm.serviceInfo = data.device.serviceInfo
                this.dataForm.passwd = data.device.passwd
                this.dataForm.systemType = data.device.systemType
                this.dataForm.serialPortType = data.device.serialPortType
                this.dataForm.temperatureHumidityLimit = data.device.temperatureHumidityLimit
                this.dataForm.uploadInterval = data.device.uploadInterval
                this.dataForm.taskHour = data.device.taskHour
                this.dataForm.taskMinute = data.device.taskMinute
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
              url: this.$http.adornUrl(`/generator/device/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'siteId': this.dataForm.siteId,
                'mn': this.dataForm.mn,
                'interfacePath': this.dataForm.interfacePath,
                'serviceInfo': this.dataForm.serviceInfo,
                'passwd': this.dataForm.passwd,
                'systemType': this.dataForm.systemType,
                'serialPortType': this.dataForm.serialPortType,
                'temperatureHumidityLimit': this.dataForm.temperatureHumidityLimit,
                'uploadInterval': this.dataForm.uploadInterval,
                'taskHour': this.dataForm.taskHour,
                'taskMinute': this.dataForm.taskMinute
                // 'createAt': this.dataForm.createAt
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
      getSiteList() {
        this.$http({
          url: this.$http.adornUrl('/generator/site/list?limit=1008611')
        }).then(({data}) => {
          if (data.page) {
            this.stieLists = data.page.list || []
          }
        })
      }
    }
  }
</script>
