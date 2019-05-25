<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="站点" prop="siteId">
        <!-- <el-input v-model="dataForm.siteId" placeholder="站点ID"></el-input> -->
        <el-select v-model="dataForm.siteId" placeholder="站点">
          <el-option v-for="(item,index) in stieList" :key="index" :label="item.site" :value="item.id"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="设备编码(MN)" prop="MN">
        <el-select v-model="dataForm.mn" placeholder="设备编码(MN)" @click.native="getMnList()">
          <el-option v-for="(item,index) in mnList" :key="index" :label="item.mn" :value="item.mn"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="请求编码(QN)" prop="qn">
        <el-input v-model="dataForm.qn" placeholder="请求编码(QN)"></el-input>
      </el-form-item>
      <el-form-item label="系统编码(ST)" prop="st">
        <el-input v-model="dataForm.st" placeholder="系统编码(ST)"></el-input>
      </el-form-item>
      <el-form-item label="命令编码(CN)" prop="cn">
        <el-input v-model="dataForm.cn" placeholder="命令编码(CN)"></el-input>
      </el-form-item>
      <el-form-item label="密码(PW)" prop="pw">
        <el-input v-model="dataForm.pw" placeholder="密码(PW)"></el-input>
      </el-form-item>
      <el-form-item label="拆分包应答标识(FLAG)" prop="flag">
        <el-input v-model="dataForm.flag" placeholder="拆分包应答标识(FLAG)"></el-input>
      </el-form-item>
      <el-form-item label="指令参数(CP)" prop="cp">
        <el-input v-model="dataForm.cp" placeholder="指令参数(CP)"></el-input>
      </el-form-item>
      <el-form-item label="数据时间" prop="dataTime">
        <!-- <el-input v-model="dataForm.startTime" placeholder="运维起始时间"></el-input> -->
        <el-date-picker
          v-model="dataForm.dataTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          type="datetime"
          placeholder="数据时间">
        </el-date-picker>
      </el-form-item>

      <el-form-item label="降尘实时数据(a34011-rtd)" prop="a34011Rtd">
        <el-input v-model="dataForm.a34011Rtd" placeholder="降尘实时数据(a34011-rtd)"></el-input>
      </el-form-item>
      <el-form-item label="降尘原始数据(a34011-ori)" prop="a34011Ori">
        <el-input v-model="dataForm.a34011Ori" placeholder="降尘原始数据(a34011-ori)"></el-input>
      </el-form-item>
      <el-form-item label="降尘数据标识位(a34011-flag)" prop="a34011Flag">
        <el-input v-model="dataForm.a34011Flag" placeholder="降尘数据标识位(a34011-flag)"></el-input>
      </el-form-item>
      <el-form-item label="有效天数(a34011-day)" prop="a34011Day">
        <el-input v-model="dataForm.a34011Day" placeholder="降尘数据标识位(a34011-day)"></el-input>
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
    data() {
      return {
        visible: false,
        stieList: [],
        mnList: [],
        dataForm: {
          id: 0,
          qn: '',
          mn: '',
          st: '',
          cn: '',
          pw: '',
          flag: '',
          cp: '',
          dataTime: '',
          a34011Rtd: '',
          a34011Ori: '',
          a34011Flag: '',
          a34011Day: ''
        },
        dataRule: {
          qn: [
            {required: true, message: '请求编码(QN)不能为空', trigger: 'blur'}
          ],
          mn: [
            {required: true, message: '设备编码(MN)不能为空', trigger: 'blur'}
          ],
          st: [
            {required: true, message: '系统编码(ST)不能为空', trigger: 'blur'}
          ],
          cn: [
            {required: true, message: '命令编码(CN)不能为空', trigger: 'blur'}
          ],
          pw: [
            {required: true, message: '密码(PW)不能为空', trigger: 'blur'}
          ],
          flag: [
            {required: true, message: '拆分包应答标识(FLAG)不能为空', trigger: 'blur'}
          ],
          cp: [
            {required: true, message: '指令参数(CP)不能为空', trigger: 'blur'}
          ],
          dataTime: [
            {required: true, message: '数据时间不能为空', trigger: 'blur'}
          ],
          a34011Rtd: [
            {required: true, message: '降尘实时数据(a34011-rtd)不能为空', trigger: 'blur'}
          ],
          a34011Ori: [
            {required: true, message: '降尘原始数据(a34011-ori)不能为空', trigger: 'blur'}
          ],
          a34011Flag: [
            {required: true, message: '降尘数据标识位(a34011-ori)不能为空', trigger: 'blur'}
          ],
          a34011Day: [
            {required: true, message: '有效天数(a34011-day)不能为空', trigger: 'blur'}
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
              url: this.$http.adornUrl(`/generator/devicedata/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.qn = data.deviceData.qn
                this.dataForm.mn = data.deviceData.mn
                this.dataForm.st = data.deviceData.st
                this.dataForm.cn = data.deviceData.cn
                this.dataForm.pw = data.deviceData.pw
                this.dataForm.flag = data.deviceData.flag
                this.dataForm.cp = data.deviceData.cp
                this.dataForm.dataTime = data.deviceData.dataTime
                this.dataForm.a34011Rtd = data.deviceData.a34011Rtd
                this.dataForm.a34011Ori = data.deviceData.a34011Ori
                this.dataForm.a34011Flag = data.deviceData.a34011Flag
                this.dataForm.a34011Day = data.deviceData.a34011Day
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
              url: this.$http.adornUrl(`/generator/devicedata/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'qn': this.dataForm.qn,
                'mn': this.dataForm.mn,
                'st': this.dataForm.st,
                'cn': this.dataForm.cn,
                'pw': this.dataForm.pw,
                'flag': this.dataForm.flag,
                'cp': this.dataForm.cp,
                'dataTime': this.dataForm.dataTime,
                'a34011Rtd': this.dataForm.a34011Rtd,
                'a34011Ori': this.dataForm.a34011Ori,
                'a34011Flag': this.dataForm.a34011Flag,
                'a34011Day': this.dataForm.a34011Day
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
            this.stieList = data.page.list || []
          }
        })
      },
      getMnList() {
        this.$http({
          url: this.$http.adornUrl(`/generator/device/list?siteId=${this.dataForm.siteId}`)
        }).then(({data}) => {
          if (data.page) {
            this.mnList = data.page.list || []
          }
        })
      }
    }
  }
</script>
