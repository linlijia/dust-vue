<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '操作'"
    :close-on-click-modal="false"
    width="624px"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="120px">

      <!--<el-form-item label="问去" prop="taskId">-->
      <!--<el-input v-model="dataForm.taskId" placeholder="task"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item label="站点" prop="siteId">-->
      <!--<el-input v-model="dataForm.siteId" placeholder="站点"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item label="运维人员" prop="opsUser">-->
      <!--&lt;!&ndash; <el-input v-model="dataForm.opsUser" placeholder="运维人员"></el-input> &ndash;&gt;-->
      <!--<el-select v-model="dataForm.opsUser" placeholder="运维人员">-->
      <!--<el-option v-for="(item,index) in userList" :key="index" :label="item.username" :value="item.userId"></el-option>-->
      <!--</el-select>-->
      <!--</el-form-item>-->
      <!--<el-form-item label="地址" prop="address">-->
      <!--<el-input v-model="dataForm.address" placeholder="地址"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item label="站点联系人" prop="siteContact">-->
      <!--<el-select v-model="dataForm.siteContact" placeholder="站点联系人">-->
      <!--<el-option v-for="(item,index) in userList" :key="index" :label="item.username" :value="item.userId"></el-option>-->
      <!--</el-select>-->
      <!--&lt;!&ndash; <el-input v-model="dataForm.siteContact" placeholder="站点联系人"></el-input> &ndash;&gt;-->
      <!--</el-form-item>-->
      <!--<el-form-item label="联系方式" prop="sitePhoneNum">-->
      <!--<el-input v-model="dataForm.sitePhoneNum" placeholder="联系方式"></el-input>-->
      <!--</el-form-item>-->
      <el-form-item label="机芯水平" prop="movementLevel">
        <el-radio
          v-model="dataForm.movementLevel"
          v-for="(item,index) in [{label:'正常', val: 1}, {label:'调整', val: 0}]"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.movementLevel" placeholder="机芯水平"></el-input> -->
      </el-form-item>
      <el-form-item label="机芯水平备注" prop="movementLevelNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.movementLevelNote">
        </el-input>
        <!-- <el-input v-model="dataForm.movementLevelNote" placeholder="机芯水平备注"></el-input> -->
      </el-form-item>
      <el-form-item label="滤膜更换" prop="filterReplacement">
        <el-radio
          v-model="dataForm.filterReplacement"
          v-for="(item,index) in [{label:'更换', val: 1}, {label:'未更换', val: 0}]"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.filterReplacement" placeholder="滤膜更换"></el-input> -->
      </el-form-item>
      <el-form-item label="滤膜更换备注" prop="filterReplacementNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.filterReplacementNote">
        </el-input>
        <!-- <el-input v-model="dataForm.filterReplacementNote" placeholder="滤膜更换备注"></el-input> -->
      </el-form-item>
      <el-form-item label="称量仓除尘" prop="weighingDust">
        <el-radio
          v-model="dataForm.weighingDust"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.weighingDust" placeholder="称量仓除尘"></el-input> -->
      </el-form-item>
      <el-form-item label="称量仓除尘备注" prop="weighingDustNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.weighingDustNote"></el-input>
        <!-- <el-input v-model="dataForm.weighingDustNote" placeholder="称量仓除尘备注"></el-input> -->
      </el-form-item>
      <el-form-item label="外部除尘" prop="externalDust">
        <el-radio
          v-model="dataForm.externalDust"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.externalDust" placeholder="外部除尘"></el-input> -->
      </el-form-item>
      <el-form-item label="外部除尘备注" prop="externalDustNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.externalDustNote"></el-input>
        <!-- <el-input v-model="dataForm.externalDustNote" placeholder="外部除尘备注"></el-input> -->
      </el-form-item>
      <el-form-item label="循环系统除尘" prop="circulatorySystemDust">
        <el-radio
          v-model="dataForm.circulatorySystemDust"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.circulatorySystemDust" placeholder="循环系统除尘"></el-input> -->
      </el-form-item>
      <el-form-item label="循环系统除尘备注" prop="circulatorySystemDustNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.circulatorySystemDustNote"></el-input>
        <!-- <el-input v-model="dataForm.circulatorySystemDustNote" placeholder="循环系统除尘备注"></el-input> -->
      </el-form-item>
      <el-form-item label="储水桶补水" prop="waterStorage">
        <el-radio
          v-model="dataForm.waterStorage"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.waterStorage" placeholder="储水桶补水"></el-input> -->
      </el-form-item>
      <el-form-item label="储水桶补水备注" prop="waterStorageNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.waterStorageNote"></el-input>
        <!-- <el-input v-model="dataForm.waterStorageNote" placeholder="储水桶补水备注"></el-input> -->
      </el-form-item>
      <el-form-item label="部件替换" prop="componentReplacement">
        <el-radio
          v-model="dataForm.componentReplacement"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.componentReplacement" placeholder="部件替换"></el-input> -->
      </el-form-item>
      <el-form-item label="部件替换备注" prop="componentReplacementNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.componentReplacementNote"></el-input>
        <!-- <el-input v-model="dataForm.componentReplacementNote" placeholder="部件替换备注"></el-input> -->
      </el-form-item>
      <el-form-item label="设备外观检查" prop="equipmentAppearanceCheck">
        <el-radio
          v-model="dataForm.equipmentAppearanceCheck"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.equipmentAppearanceCheck" placeholder="设备外观检查"></el-input> -->
      </el-form-item>
      <el-form-item label="设备外观检查备注" prop="equipmentAppearanceCheckNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.equipmentAppearanceCheckNote"></el-input>
        <!-- <el-input v-model="dataForm.equipmentAppearanceCheckNote" placeholder="设备外观检查备注"></el-input> -->
      </el-form-item>
      <el-form-item label="离场时设备运行状态" prop="deviceStatus">
        <el-radio
          v-model="dataForm.deviceStatus"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.deviceStatus" placeholder="离场时设备运行状态"></el-input> -->
      </el-form-item>
      <el-form-item label="离场使设备运行状态备注" prop="deviceStatusNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.deviceStatusNote"></el-input>
        <!-- <el-input v-model="dataForm.deviceStatusNote" placeholder="离场使设备运行状态备注"></el-input> -->
      </el-form-item>
      <el-form-item label="监测点运维几率单" prop="opsRecord">
        <el-radio
          v-model="dataForm.opsRecord"
          v-for="(item,index) in statusArr"
          :key="index"
          :label="item.val">{{item.label}}
        </el-radio>
        <!-- <el-input v-model="dataForm.opsRecord" placeholder="监测点运维记录单"></el-input> -->
      </el-form-item>
      <el-form-item label="监测点运维记录单备注" prop="opsRecordNote">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.opsRecordNote"></el-input>
        <!-- <el-input v-model="dataForm.opsRecordNote" placeholder="监测点运维记录单备注"></el-input> -->
      </el-form-item>


      <el-form-item label="上传图片" prop="fileList">
        <el-upload
          :file-list="fileList"
          style="width:470px"
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
    data() {
      return {
        statusArr: [{label: '完成', val: 1}, {label: '未完成', val: 0}],
        visible: false,
        dataForm: {
          id: 0,
          // siteId: '',
          // opsUser: '',
          // address: '',
          // siteContact: '',
          // sitePhoneNum: '',
          taskId: 0,
          movementLevel: '',
          movementLevelNote: '',
          filterReplacement: '',
          filterReplacementNote: '',
          weighingDust: '',
          weighingDustNote: '',
          externalDust: '',
          externalDustNote: '',
          circulatorySystemDust: '',
          circulatorySystemDustNote: '',
          waterStorage: '',
          waterStorageNote: '',
          componentReplacementNote: '',
          componentReplacement: '',
          equipmentAppearanceCheckNote: '',
          equipmentAppearanceCheck: '',
          deviceStatus: '',
          deviceStatusNote: '',
          opsRecord: '',
          opsRecordNote: '',
          opsImage: [],
          apiUrl: '',
          resource: '',
          fileList: []
        },
        dataRule: {
          // siteId: [
          //   { required: true, message: '站点不能为空', trigger: 'blur' }
          // ],
          // opsUser: [
          //   { required: true, message: '运维人员不能为空', trigger: 'blur' }
          // ],
          // address: [
          //   { required: true, message: '不能为空', trigger: 'blur' }
          // ],
          // siteContact: [
          //   { required: true, message: '站点联系人不能为空', trigger: 'blur' }
          // ],
          // sitePhoneNum: [
          //   { required: true, message: '联系方式不能为空', trigger: 'blur' }
          // ],
          movementLevel: [
            {required: true, message: '机芯水平不能为空', trigger: 'blur'}
          ],
          filterReplacement: [
            {required: true, message: '滤膜更换不能为空', trigger: 'blur'}
          ],
          // filterReplacementNote: [
          //   {required: true, message: '滤膜更换备注不能为空', trigger: 'blur'}
          // ],
          weighingDust: [
            {required: true, message: '称量仓除尘不能为空', trigger: 'blur'}
          ],
          // weighingDustNote: [
          //   {required: true, message: '称量仓除尘备注不能为空', trigger: 'blur'}
          // ],
          externalDust: [
            {required: true, message: '外部除尘不能为空', trigger: 'blur'}
          ],
          // externalDustNote: [
          //   {required: true, message: '外部除尘备注不能为空', trigger: 'blur'}
          // ],
          circulatorySystemDust: [
            {required: true, message: '循环系统除尘不能为空', trigger: 'blur'}
          ],
          // circulatorySystemDustNote: [
          //   {required: true, message: '循环系统除尘备注不能为空', trigger: 'blur'}
          // ],
          waterStorage: [
            {required: true, message: '储水桶补水不能为空', trigger: 'blur'}
          ],
          // waterStorageNote: [
          //   {required: true, message: '储水桶补水备注不能为空', trigger: 'blur'}
          // ],
          // componentReplacementNote: [
          //   {required: true, message: '部件替换备注不能为空', trigger: 'blur'}
          // ],
          componentReplacement: [
            {required: true, message: '部件替换不能为空', trigger: 'blur'}
          ],
          // equipmentAppearanceCheckNote: [
          //   {required: true, message: '设备外观检查备注不能为空', trigger: 'blur'}
          // ],
          equipmentAppearanceCheck: [
            {required: true, message: '设备外观检查不能为空', trigger: 'blur'}
          ],
          deviceStatus: [
            {required: true, message: '离场时设备运行状态不能为空', trigger: 'blur'}
          ],
          // deviceStatusNote: [
          //   {required: true, message: '离场使设备运行状态备注不能为空', trigger: 'blur'}
          // ],
          opsRecord: [
            {required: true, message: '监测点运维几率单不能为空', trigger: 'blur'}
          ],
          // opsRecordNote: [
          //   {required: true, message: '监测点运维记录单备注不能为空', trigger: 'blur'}
          // ]
        },

        userList: [],
        fileList: [
          // {
          //       name: 'xxx',
          //       url: 'https://tva3.sinaimg.cn/crop.0.0.750.750.180/005BOKE5jw8f26vv4x3vpj30ku0kvwfq.jpg'
          //   }, {
          //       name: 'xxx',
          //       url: 'https://tva3.sinaimg.cn/crop.0.0.750.750.180/005BOKE5jw8f26vv4x3vpj30ku0kvwfq.jpg'
          //   }, {
          //       name: 'xxx',
          //       url: 'https://tva3.sinaimg.cn/crop.0.0.750.750.180/005BOKE5jw8f26vv4x3vpj30ku0kvwfq.jpg'
          //   }
        ]
      }
    },
    mounted() {
      // this.getUserList()
    },
    methods: {
      init(id, source) {
        // 从运维记录-编辑跳转过来的，source='opsRecord'，从运维任务-操作跳转过来的 source 为null。
        // 运维记录-编辑 ：填充数据从/generator/opsrecord/info/!{id}
        // 运维任务-操作： 填充数据/generator/opsrecord/${taskId}
        this.dataForm.resource = source
        if (source && source === 'opsRecord') {
          this.dataForm.id = id || 0
          this.dataForm.apiUrl = `info/${this.dataForm.id}`
        } else {
          this.dataForm.taskId = id || 0
          this.dataForm.apiUrl = `byTaskId/${this.dataForm.taskId}`
        }
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          this.$http({
            url: this.$http.adornUrl(`/generator/opsrecord/${this.dataForm.apiUrl}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            this.fileList = []
            this.opsImage = []
            if (data && data.code === 0 && data.opsRecord != null) {
              // this.dataForm.siteId = opsRecord.siteId
              // this.dataForm.opsUser = opsRecord.opsUser
              // this.dataForm.address = opsRecord.address
              // this.dataForm.siteContact = opsRecord.siteContact
              // this.dataForm.sitePhoneNum = opsRecord.sitePhoneNum
              var opsRecord = ''
              if (this.dataForm.resource && this.dataForm.resource === 'opsRecord') {
                opsRecord = data.opsRecord
              } else {
                // 一个运维任务可能有多个记录，返回数组取第一条
                if (Array.isArray(data.opsRecord)) {
                  opsRecord = data.opsRecord[0]
                } else {
                  opsRecord = data.opsRecord
                }
              }
              this.dataForm.id = opsRecord.id
              this.dataForm.movementLevel = opsRecord.movementLevel
              this.dataForm.movementLevelNote = opsRecord.movementLevelNote
              this.dataForm.filterReplacement = opsRecord.filterReplacement
              this.dataForm.filterReplacementNote = opsRecord.filterReplacementNote
              this.dataForm.weighingDust = opsRecord.weighingDust
              this.dataForm.weighingDustNote = opsRecord.weighingDustNote
              this.dataForm.externalDust = opsRecord.externalDust
              this.dataForm.externalDustNote = opsRecord.externalDustNote
              this.dataForm.circulatorySystemDust = opsRecord.circulatorySystemDust
              this.dataForm.circulatorySystemDustNote = opsRecord.circulatorySystemDustNote
              this.dataForm.waterStorage = opsRecord.waterStorage
              this.dataForm.waterStorageNote = opsRecord.waterStorageNote
              this.dataForm.componentReplacementNote = opsRecord.componentReplacementNote
              this.dataForm.componentReplacement = opsRecord.componentReplacement
              this.dataForm.equipmentAppearanceCheckNote = opsRecord.equipmentAppearanceCheckNote
              this.dataForm.equipmentAppearanceCheck = opsRecord.equipmentAppearanceCheck
              this.dataForm.deviceStatus = opsRecord.deviceStatus
              this.dataForm.deviceStatusNote = opsRecord.deviceStatusNote
              this.dataForm.opsRecord = opsRecord.opsRecord
              this.dataForm.opsRecordNote = opsRecord.opsRecordNote
              this.dataForm.opsImage = opsRecord.opsImage
              if (this.dataForm.resource === 'opsRecord') {
                this.dataForm.taskId = opsRecord.taskId
              } else {
                this.dataForm.taskId = this.dataForm.taskId === 0 ? opsRecord.taskId : this.dataForm.taskId
              }
              this.fileList = opsRecord.opsImage.map(i => {
                return {
                  'id': i.id,
                  'url': i.filePath
                }
              })
            } else {
              this.dataForm.opsImage = []
              this.fileList = []
              this.dataForm.id = 0
            }
          })
        })
      },
      handleRemove(file, fileList) {
        this.fileList = fileList
        if (!file.id) {
          return
        }
        this.dataForm.opsImage = this.dataForm.opsImage.filter(f => f.id !== file.id)
        this.$http({
          url: this.$http.adornUrl(`/generator/opsimage/delete`),
          method: 'post',
          data: [file.id],
          headers: {
            'Content-Type': 'application/json; charset=utf-8'
          }
        })
      },
      uploadFunc(file) {
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
          this.fileList.push({
            id: data.data.id,
            url: data.data.filePath
          })
          this.dataForm.opsImage.push(data.data)
          console.log(data.data)
          console.log(this.dataForm.opsImage)
        })

        return false;
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            // var opsImageList = this.fileList.map(i => {
            //   console.log(i)
            //   return {
            //     'id': i.id
            //   }
            // })
            let subAction = ''
            if (this.dataForm.resource === 'opsRecord') {
              subAction = `${!this.dataForm.id ? 'save' : 'update'}`
            } else {
              subAction = `${!this.dataForm.taskId ? 'save' : 'update'}`
            }
            this.$http({
              url: this.$http.adornUrl(`/generator/opsrecord/${subAction}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                // 'siteId': this.dataForm.siteId,
                // 'opsUser': this.dataForm.opsUser,
                // 'address': this.dataForm.address,
                // 'siteContact': this.dataForm.siteContact,
                // 'sitePhoneNum': this.dataForm.sitePhoneNum,
                'taskId': this.dataForm.taskId,
                'movementLevel': this.dataForm.movementLevel,
                'movementLevelNote': this.dataForm.movementLevelNote,
                'filterReplacement': this.dataForm.filterReplacement,
                'filterReplacementNote': this.dataForm.filterReplacementNote,
                'weighingDust': this.dataForm.weighingDust,
                'weighingDustNote': this.dataForm.weighingDustNote,
                'externalDust': this.dataForm.externalDust,
                'externalDustNote': this.dataForm.externalDustNote,
                'circulatorySystemDust': this.dataForm.circulatorySystemDust,
                'circulatorySystemDustNote': this.dataForm.circulatorySystemDustNote,
                'waterStorage': this.dataForm.waterStorage,
                'waterStorageNote': this.dataForm.waterStorageNote,
                'componentReplacementNote': this.dataForm.componentReplacementNote,
                'componentReplacement': this.dataForm.componentReplacement,
                'equipmentAppearanceCheckNote': this.dataForm.equipmentAppearanceCheckNote,
                'equipmentAppearanceCheck': this.dataForm.equipmentAppearanceCheck,
                'deviceStatus': this.dataForm.deviceStatus,
                'deviceStatusNote': this.dataForm.deviceStatusNote,
                'opsRecord': this.dataForm.opsRecord,
                'opsRecordNote': this.dataForm.opsRecordNote,
                'opsImage': this.dataForm.opsImage
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
      // getUserList () {
      //   this.$http({
      //       url : this.$http.adornUrl('/sys/user/list')
      //   }).then(({data}) => {
      //       if(data.page) {
      //           this.userList = data.page.list || []
      //       }
      //   })
      // }
    }
  }
</script>
