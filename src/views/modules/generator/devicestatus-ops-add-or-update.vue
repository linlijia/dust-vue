<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <!-- <el-form-item label="请求编码" prop="qn">
      <el-input v-model="dataForm.qn" placeholder="请求编码"></el-input>
    </el-form-item> -->
    <el-form-item label="设备编码" prop="mn">
      <el-input v-model="dataForm.mn" placeholder="设备编码"></el-input>
    </el-form-item>
    <!-- <el-form-item label="系统编码" prop="st">
      <el-input v-model="dataForm.st" placeholder="系统编码"></el-input>
    </el-form-item>
    <el-form-item label="命令编码" prop="cn">
      <el-input v-model="dataForm.cn" placeholder="命令编码"></el-input>
    </el-form-item> -->
    <el-form-item label="数据时间" prop="dataTime">
      <el-input v-model="dataForm.dataTime" placeholder="数据时间"></el-input>
    </el-form-item>
    <!-- <el-form-item label="密码" prop="pw">
      <el-input v-model="dataForm.pw" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item label="拆分包应答标识" prop="flag">
      <el-input v-model="dataForm.flag" placeholder="拆分包应答标识"></el-input>
    </el-form-item>
    <el-form-item label="指令参数" prop="cp">
      <el-input v-model="dataForm.cp" placeholder="指令参数"></el-input>
    </el-form-item> -->
    <el-form-item label="X轴偏移量" prop="s10001">
      <el-input v-model="dataForm.s10001" placeholder="X轴偏移量"></el-input>
    </el-form-item>
    <el-form-item label="Y轴偏移量" prop="s10002">
      <el-input v-model="dataForm.s10002" placeholder="Y轴偏移量"></el-input>
    </el-form-item>
    <el-form-item label="热盘温度" prop="s10003">
      <el-input v-model="dataForm.s10003" placeholder="热盘温度"></el-input>
    </el-form-item>
    <el-form-item label="降尘室温度" prop="s10004">
      <el-input v-model="dataForm.s10004" placeholder="降尘室温度"></el-input>
    </el-form-item>
    <el-form-item label="降尘室温度" prop="s10005">
      <el-input v-model="dataForm.s10005" placeholder="降尘室温度"></el-input>
    </el-form-item>
    <el-form-item label="测量室温度" prop="s10006">
      <el-input v-model="dataForm.s10006" placeholder="测量室温度"></el-input>
    </el-form-item>
    <el-form-item label="测量室湿度" prop="s10007">
      <el-input v-model="dataForm.s10007" placeholder="测量室湿度"></el-input>
    </el-form-item>
    <el-form-item label="电源电压" prop="s10008">
      <el-input v-model="dataForm.s10008" placeholder="电源电压"></el-input>
    </el-form-item>
    <el-form-item label="电池电压" prop="s10009">
      <el-input v-model="dataForm.s10009" placeholder="电池电压"></el-input>
    </el-form-item>
    <el-form-item label="经度" prop="s10010">
      <el-input v-model="dataForm.s10010" placeholder="经度"></el-input>
    </el-form-item>
    <el-form-item label="纬度" prop="s10011">
      <el-input v-model="dataForm.s10011" placeholder="纬度"></el-input>
    </el-form-item>
    <el-form-item label="高度" prop="s10012">
      <el-input v-model="dataForm.s10012" placeholder="高度"></el-input>
    </el-form-item>
    <el-form-item label="门状态" prop="s10013">
      <el-input v-model="dataForm.s10013" placeholder="门状态"></el-input>
    </el-form-item>
    <el-form-item label="托盘状态" prop="s10014">
      <el-input v-model="dataForm.s10014" placeholder="托盘状态"></el-input>
    </el-form-item>
    <el-form-item label="降水状态" prop="s10015">
      <el-input v-model="dataForm.s10015" placeholder="降水状态"></el-input>
    </el-form-item>
    <el-form-item label="水箱水位状态" prop="s10016">
      <el-input v-model="dataForm.s10016" placeholder="水箱水位状态"></el-input>
    </el-form-item>
    <el-form-item label="机芯上电状态" prop="s10017">
      <el-input v-model="dataForm.s10017" placeholder="机芯上电状态"></el-input>
    </el-form-item>
    <el-form-item label="机芯校准状态" prop="s10018">
      <el-input v-model="dataForm.s10018" placeholder="机芯校准状态"></el-input>
    </el-form-item>
    <el-form-item label="操作" prop="operation">
      <el-input v-model="dataForm.operation" placeholder="操作"></el-input>
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
          qn: '',
          mn: '',
          st: '',
          cn: '',
          dataTime: '',
          pw: '',
          flag: '',
          cp: '',
          s10001: '',
          s10002: '',
          s10003: '',
          s10004: '',
          s10005: '',
          s10006: '',
          s10007: '',
          s10008: '',
          s10009: '',
          s10010: '',
          s10011: '',
          s10012: '',
          s10013: '',
          s10014: '',
          s10015: '',
          s10016: '',
          s10017: '',
          s10018: '',
          operation: ''
        },
        dataRule: {
          // qn: [
          //   { required: true, message: '请求编码不能为空', trigger: 'blur' }
          // ],
          mn: [
            { required: true, message: '设备编码不能为空', trigger: 'blur' }
          ],
          // st: [
          //   { required: true, message: '系统编码不能为空', trigger: 'blur' }
          // ],
          // cn: [
          //   { required: true, message: '命令编码不能为空', trigger: 'blur' }
          // ],
          dataTime: [
            { required: true, message: '数据时间不能为空', trigger: 'blur' }
          ],
          // pw: [
          //   { required: true, message: '密码不能为空', trigger: 'blur' }
          // ],
          // flag: [
          //   { required: true, message: '拆分包应答标识不能为空', trigger: 'blur' }
          // ],
          // cp: [
          //   { required: true, message: '指令参数不能为空', trigger: 'blur' }
          // ],
          s10001: [
            { required: true, message: 'X轴偏移量不能为空', trigger: 'blur' }
          ],
          s10002: [
            { required: true, message: 'Y轴偏移量不能为空', trigger: 'blur' }
          ],
          s10003: [
            { required: true, message: '热盘温度不能为空', trigger: 'blur' }
          ],
          s10004: [
            { required: true, message: '降尘室温度不能为空', trigger: 'blur' }
          ],
          s10005: [
            { required: true, message: '降尘室温度不能为空', trigger: 'blur' }
          ],
          s10006: [
            { required: true, message: '测量室温度不能为空', trigger: 'blur' }
          ],
          s10007: [
            { required: true, message: '测量室湿度不能为空', trigger: 'blur' }
          ],
          s10008: [
            { required: true, message: '电源电压不能为空', trigger: 'blur' }
          ],
          s10009: [
            { required: true, message: '电池电压不能为空', trigger: 'blur' }
          ],
          s10010: [
            { required: true, message: '经度不能为空', trigger: 'blur' }
          ],
          s10011: [
            { required: true, message: '纬度不能为空', trigger: 'blur' }
          ],
          s10012: [
            { required: true, message: '高度不能为空', trigger: 'blur' }
          ],
          s10013: [
            { required: true, message: '门状态不能为空', trigger: 'blur' }
          ],
          s10014: [
            { required: true, message: '托盘状态不能为空', trigger: 'blur' }
          ],
          s10015: [
            { required: true, message: '降水状态不能为空', trigger: 'blur' }
          ],
          s10016: [
            { required: true, message: '水箱水位状态不能为空', trigger: 'blur' }
          ],
          s10017: [
            { required: true, message: '机芯上电状态不能为空', trigger: 'blur' }
          ],
          s10018: [
            { required: true, message: '机芯校准状态不能为空', trigger: 'blur' }
          ],
          operation: [
            { required: true, message: '操作不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/devicestatus/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                // this.dataForm.qn = data.deviceStatus.qn
                this.dataForm.mn = data.deviceStatus.mn
                // this.dataForm.st = data.deviceStatus.st
                // this.dataForm.cn = data.deviceStatus.cn
                this.dataForm.dataTime = data.deviceStatus.dataTime
                // this.dataForm.pw = data.deviceStatus.pw
                // this.dataForm.flag = data.deviceStatus.flag
                // this.dataForm.cp = data.deviceStatus.cp
                this.dataForm.s10001 = data.deviceStatus.s10001
                this.dataForm.s10002 = data.deviceStatus.s10002
                this.dataForm.s10003 = data.deviceStatus.s10003
                this.dataForm.s10004 = data.deviceStatus.s10004
                this.dataForm.s10005 = data.deviceStatus.s10005
                this.dataForm.s10006 = data.deviceStatus.s10006
                this.dataForm.s10007 = data.deviceStatus.s10007
                this.dataForm.s10008 = data.deviceStatus.s10008
                this.dataForm.s10009 = data.deviceStatus.s10009
                this.dataForm.s10010 = data.deviceStatus.s10010
                this.dataForm.s10011 = data.deviceStatus.s10011
                this.dataForm.s10012 = data.deviceStatus.s10012
                this.dataForm.s10013 = data.deviceStatus.s10013
                this.dataForm.s10014 = data.deviceStatus.s10014
                this.dataForm.s10015 = data.deviceStatus.s10015
                this.dataForm.s10016 = data.deviceStatus.s10016
                this.dataForm.s10017 = data.deviceStatus.s10017
                this.dataForm.s10018 = data.deviceStatus.s10018
                this.dataForm.operation = data.deviceStatus.operation
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
              url: this.$http.adornUrl(`/generator/devicestatus/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                // 'qn': this.dataForm.qn,
                'mn': this.dataForm.mn,
                // 'st': this.dataForm.st,
                // 'cn': this.dataForm.cn,
                'dataTime': this.dataForm.dataTime,
                // 'pw': this.dataForm.pw,
                // 'flag': this.dataForm.flag,
                // 'cp': this.dataForm.cp,
                's10001': this.dataForm.s10001,
                's10002': this.dataForm.s10002,
                's10003': this.dataForm.s10003,
                's10004': this.dataForm.s10004,
                's10005': this.dataForm.s10005,
                's10006': this.dataForm.s10006,
                's10007': this.dataForm.s10007,
                's10008': this.dataForm.s10008,
                's10009': this.dataForm.s10009,
                's10010': this.dataForm.s10010,
                's10011': this.dataForm.s10011,
                's10012': this.dataForm.s10012,
                's10013': this.dataForm.s10013,
                's10014': this.dataForm.s10014,
                's10015': this.dataForm.s10015,
                's10016': this.dataForm.s10016,
                's10017': this.dataForm.s10017,
                's10018': this.dataForm.s10018,
                'operation': this.dataForm.operation
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
