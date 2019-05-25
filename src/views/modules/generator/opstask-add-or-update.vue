<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="120px">

      <el-form-item label="站点" prop="sites">
        <!-- <el-input v-model="dataForm.siteId" placeholder="站点ID"></el-input> -->
        <el-select v-model="dataForm.sites" multiple value-key="id" placeholder="站点">
          <el-option v-for="item in stieList" :key="item.id" :label="item.site"
                     :value="item"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="任务类型" prop="taskType">
        <el-select v-model="dataForm.taskType"  value-key="taskType" placeholder="任务类型">
          <el-option v-for="item in taskTypeList" :key="item.lable" :label="item.value"
                     :value="item.value"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="执行日期" prop="opsDate">
        <!-- <el-input v-model="dataForm.opsDate" placeholder="执行日期"></el-input> -->
        <el-date-picker
          v-model="dataForm.opsDate"
          value-format="yyyy-MM-dd HH:mm:ss"
          type="date"
          :picker-options="pickerOptions"
          placeholder="选择执行日期">
        </el-date-picker>
      </el-form-item>

      <el-form-item label="运维人员" prop="opsUsers">
        <!-- <el-input v-model="dataForm.opsUser" placeholder="运维人员"></el-input> -->
        <el-select v-model="dataForm.opsUsers" multiple value-key="userId" placeholder="运维人员">
          <el-option v-for="item in userList" :key="item.userId" :label="item.name"
                     :value="item"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="任务简介" prop="note">
        <el-input v-model="dataForm.note" placeholder="任务简介"></el-input>
      </el-form-item>
      <!--<el-form-item label="任务状态" prop="opsStatus">-->
      <!--<el-input v-model="dataForm.opsStatus" placeholder="任务状态"></el-input>-->
      <!--</el-form-item>-->
      <el-form-item label="运维起始时间" prop="startTime">
        <!-- <el-input v-model="dataForm.startTime" placeholder="运维起始时间"></el-input> -->
        <el-date-picker
          v-model="dataForm.startTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          type="datetime"
          placeholder="选择运维起始时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="运维截止时间" prop="endTime">
        <!-- <el-input v-model="dataForm.endTime" placeholder="运维截止时间"></el-input> -->
        <el-date-picker
          v-model="dataForm.endTime"
          value-format="yyyy-MM-dd HH:mm:ss"
          type="datetime"
          placeholder="选择运维截止时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="出行方式" prop="travelMode">
        <el-select v-model="dataForm.travelMode" value-key="value" placeholder="出行方式">
          <el-option v-for="item in travelTool" :key="item.label" :label="item.value"
                     :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <!--<el-form-item label="签到时间" prop="checkIn">-->
      <!--<el-input v-model="dataForm.checkIn" placeholder="签到时间"></el-input>-->
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
        dataForm: {
          id: 0,
          sites: '',
          taskType: '',
          assigner: '',
          opsUsers: '',
          note: '',
          opsStatus: '',
          startTime: '',
          endTime: '',
          travelMode: '',
          checkIn: '',
          opsDate: ''
        },
        dataRule: {
          sites: [
            {required: true, message: '站点组合不能为空', trigger: 'blur'}
          ],
          opsUsers: [
            {required: true, message: '运维人员不能为空', trigger: 'blur'}
          ],
          travelMode: [
            {required: true, message: '出行方式不能为空', trigger: 'blur'}
          ],
        },

        userList: [],
        stieList: [],
        travelTool: [],
        taskTypeList: [],
        pickerOptions: {
          disabledDate(time) {
            return time.getTime() < Date.now() - 8.64e7;
          }
        }
      }
    },
    mounted() {
      this.getInitData()
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/opstask/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.sites = data.opsTask.sites
                this.dataForm.taskType = data.opsTask.opsTask.taskType
                this.dataForm.assigner = data.opsTask.opsTask.assigner
                this.dataForm.opsUsers = data.opsTask.opsUsers
                this.dataForm.note = data.opsTask.opsTask.note
                this.dataForm.opsStatus = data.opsTask.opsTask.opsStatus
                this.dataForm.startTime = data.opsTask.opsTask.startTime
                this.dataForm.endTime = data.opsTask.opsTask.endTime
                this.dataForm.travelMode = data.opsTask.opsTask.travelMode
                this.dataForm.checkIn = data.opsTask.opsTask.checkIn
                this.dataForm.createAt = data.opsTask.opsTask.createAt
                this.dataForm.opsDate = data.opsTask.opsTask.opsDate
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
              url: this.$http.adornUrl(`/generator/opstask/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData(
                {
                  'opsUsers': this.dataForm.opsUsers,
                  'sites': this.dataForm.sites,
                  'opsTask': {
                    'id': this.dataForm.id || undefined,
                    'taskType': this.dataForm.taskType,
                    'assigner': this.dataForm.assigner,
                    'note': this.dataForm.note,
                    'opsStatus': this.dataForm.opsStatus,
                    'startTime': this.dataForm.startTime,
                    'endTime': this.dataForm.endTime,
                    'travelMode': this.dataForm.travelMode,
                    'checkIn': this.dataForm.checkIn,
                    'createAt': this.dataForm.createAt,
                    'opsDate': this.dataForm.opsDate
                  }
                }
              )
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
      getInitData() {
        this.getSiteList()
        this.getUserList()
        this.getTravelTool()
        this.getTaskType()
      },
      getUserList() {
        this.$http({
          url: this.$http.adornUrl('/sys/user/list')
        }).then(({data}) => {
          if (data.page) {
            this.userList = data.page.list || []
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
      getTravelTool() {
        this.travelTool = [{
          value: '公交',
          label: '公交'
        }, {
          value: '打车',
          label: '打车'
        }, {
          value: '公司用车',
          label: '公司用车'
        }]
      },
      getTaskType() {
        this.taskTypeList = [{
          value: '常规运维',
          label: '常规运维'
        }, {
          value: '应急运维',
          label: '应急运维'
        }, {
          value: '设备安装',
          label: '设备安装'
        }]
      }
    }
  }
</script>
