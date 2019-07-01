<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="站点" prop="siteId">
        <el-select v-model="dataForm.siteId" placeholder="站点">
          <el-option v-for="(item,index) in stieList" :key="index" :label="item.site" :value="item.id"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="设备编码(MN)" prop="mn">
        <el-select v-model="dataForm.mn" placeholder="设备编码(MN)" @click.native="getMnList()">
          <el-option v-for="(item,index) in mnList" :key="index" :label="item.mn" :value="item.mn"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="发生时间" prop="happenTime">
        <!-- <el-input v-model="dataForm.startTime" placeholder="运维起始时间"></el-input> -->
        <el-date-picker
          v-model="dataForm.happenTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          type="datetime"
          placeholder="发生时间">
        </el-date-picker>
      </el-form-item>
      <!--<el-form-item label="故障编码" prop="troubleCode">-->
      <!--<el-input v-model="dataForm.troubleCode" placeholder="" :disabled="true"></el-input>-->
      <!--</el-form-item>-->
      <el-form-item label="故障类型" prop="troubleCode">
        <el-select v-model="dataForm.troubleCode">
          <el-option v-for="(i, idx) in troubleCodeList" :key="idx" :value="i.value" :label="i.label"/>
        </el-select>
      </el-form-item>

      <el-form-item label="故障描述" prop="troubleDescription">
        <el-input v-model="dataForm.troubleDescription" placeholder=""></el-input>
      </el-form-item>

      <el-form-item label="故障处理状态" prop="solved">
        <el-radio
          v-model="dataForm.solved"
          v-for="(item,index) in solvedAtrr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
      </el-form-item>
      <el-form-item label="解决时间" prop="solvedTime">
        <el-date-picker
          v-model="dataForm.solvedTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          type="datetime"
          placeholder="解决时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="处理方式" prop="solvedMethod">
        <el-select v-model="dataForm.solvedMethod">
          <el-option v-for="(i,idx) in solvedMethodList" :key="idx" :value="i.val" :label="i.label"/>
        </el-select>
      </el-form-item>

      <el-form-item label="解决人员" prop="troubleShooter">
        <!-- <el-input v-model="dataForm.opsUser" placeholder="运维人员"></el-input> -->
        <el-select v-model="dataForm.troubleShooter" placeholder="解决人员">
          <el-option v-for="(i, idx) in userList" :key="idx" :value="i.userId" :label="i.name"/>
          <!--<el-option v-for="item in userList" :key="item.userId" :label="item.name"-->
          <!--:value="item.userId"></el-option>-->
        </el-select>
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
        dataForm: {
          id: 0,
          happenTime: '',
          mn: '',
          troubleCode: '',
          troubleDescription: '',
          solved: '',
          solvedTime: '',
          solvedMethod: '',
          troubleShooter: '',
          siteId: '',
          stieList: [],
          mnList: [],
          troubleTypes: []
        },
        stieList: [],
        mnList: [],
        solvedAtrr: [{label: '已解决', val: 1}, {label: '未解决', val: 0}],
        troubleCodeList: [],
        troubleTypes: [],
        userList: [],
        solvedMethodList: [{label: '设备自修复', val: 0}, {label: '远程处理', val: 1}, {label: '现场处理', val: 2}],
        dataRule: {
          happenTime: [
            {required: true, message: '发生时间不能为空', trigger: 'blur'}
          ],
          mn: [
            {required: true, message: '设备编码不能为空', trigger: 'blur'}
          ],
          troubleTypes: [
            {required: true, message: '故障类型不能为空', trigger: 'blur'}
          ],
          troubleCode: [
            {required: true, message: '问题编码不能为空', trigger: 'blur'}
          ],
          troubleDescription: [
            {required: true, message: '问题描述不能为空', trigger: 'blur'}
          ],
          solved: [
            {required: true, message: '是否解决不能为空', trigger: 'blur'}
          ],
          solvedTime: [
            {required: true, message: '解决时间不能为空', trigger: 'blur'}
          ],
          solvedMethod: [
            {required: true, message: '处理方式不能为空', trigger: 'blur'}
          ],
          troubleShooter: [
            {required: true, message: '故障解决人员不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    mounted() {
      this.getSiteList()
      this.getTroubleTypsList()
      this.getUserList()
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/trouble/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.happenTime = data.trouble.happenTime
                this.dataForm.mn = data.trouble.mn
                this.dataForm.troubleCode = data.trouble.troubleCode
                this.dataForm.troubleDescription = data.trouble.troubleDescription
                this.dataForm.solved = data.trouble.solved
                this.dataForm.solvedTime = data.trouble.solvedTime
                this.dataForm.solvedMethod = data.trouble.solvedMethod
                this.dataForm.troubleShooter = data.trouble.troubleShooter
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
              url: this.$http.adornUrl(`/generator/trouble/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'happenTime': this.dataForm.happenTime,
                'mn': this.dataForm.mn,
                'troubleCode': this.dataForm.troubleCode,
                'troubleDescription': this.dataForm.troubleDescription,
                'solved': this.dataForm.solved,
                'solvedTime': this.dataForm.solvedTime,
                'solvedMethod': this.dataForm.solvedMethod,
                'troubleShooter': this.dataForm.troubleShooter
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
      },
      // 获取异常状态列表
      getTroubleTypsList() {
        this.$http({
          url: this.$http.adornUrl('/generator/trouble/type'),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.troubleCodeList = data.data
          }
        })
      },
      getUserList() {
        this.$http({
          url: this.$http.adornUrl('/sys/user/list?limit=1008611')
        }).then(({data}) => {
          if (data.page) {
            this.userList = data.page.list || []
          }
        })
      }
    }
  }
</script>
