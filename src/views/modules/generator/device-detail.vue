<template>
  <el-dialog
    title="查看详情"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="170px">
    <el-form-item label="站点" prop="site">
      <span>{{dataForm.site}}</span>
    </el-form-item>
    <el-form-item label="MN码" prop="mn">
      <span>{{dataForm.mn}}</span>
    </el-form-item>
    <el-form-item label="接口根路径" prop="interfacePath">
      <span>{{dataForm.interfacePath}}</span>
    </el-form-item>
    <el-form-item label="服务端信息" prop="serviceInfo">
      <span>{{dataForm.serviceInfo}}</span>
    </el-form-item>
    <el-form-item label="服务端密码" prop="passwd">
      <span>{{dataForm.passwd}}</span>
    </el-form-item>
    <el-form-item label="系统类型" prop="systemType">
      <span>{{dataForm.systemType}}</span>
    </el-form-item>
    <el-form-item label="串口名" prop="serialPortType">
      <span>{{dataForm.serialPortType}}</span>
    </el-form-item>
    <el-form-item label="温度湿度限制" prop="temperatureHumidityLimit">
      <span>{{dataForm.temperatureHumidityLimit}}</span>
    </el-form-item>
    <el-form-item label="称量间隔（小时）" prop="uploadInterval">
      <span>{{dataForm.uploadInterval}}</span>
    </el-form-item>

    <el-form-item label="启动任务小时" prop="taskHour">
      <span>{{dataForm.taskHour}}</span>
    </el-form-item>
    <el-form-item label="启动任务分钟" prop="taskMinute">
      <span>{{dataForm.taskMinute}}</span>
    </el-form-item>

    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false" type="primary">关闭</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
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
          taskMinute: ''
        }
      }
    },
    mounted () {
      this.getSiteList()
    },
    methods: {
      init (id) {
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
      getSiteList () {
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
