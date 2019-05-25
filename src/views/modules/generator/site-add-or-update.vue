<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="站点" prop="site">
      <el-input v-model="dataForm.site" placeholder="站点"></el-input>
    </el-form-item>
    <!--<el-form-item label="MN编码" prop="mn">-->
      <!--<el-input v-model="dataForm.mn" placeholder="MN编码"></el-input>-->
    <!--</el-form-item>-->
      <el-form-item label="城市" prop="city">
        <el-input v-model="dataForm.city" placeholder="城市"></el-input>
      </el-form-item>
    <el-form-item label="区域" prop="district">
      <el-input v-model="dataForm.district" placeholder="区域"></el-input>
    </el-form-item>
    <!-- <el-form-item label="国家站点" prop="superiorSite">
      <el-input v-model="dataForm.superiorSite" placeholder="国家站点"></el-input>
    </el-form-item> -->
    <el-form-item label="地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="地址"></el-input>
    </el-form-item>
    <el-form-item label="经度" prop="latidute">
      <el-input v-model="dataForm.latidute" placeholder="经度"></el-input>
    </el-form-item>
    <el-form-item label="纬度" prop="longidute">
      <el-input v-model="dataForm.longidute" placeholder="纬度"></el-input>
    </el-form-item>
    <el-form-item label="联系人" prop="contact">
      <el-input v-model="dataForm.contact" placeholder="联系人"></el-input>
    </el-form-item>
    <el-form-item label="联系电话" prop="phoneNo">
      <el-input v-model="dataForm.phoneNo" placeholder="联系电话"></el-input>
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
          site: '',
          city: '',
          district: '',
          // superiorSite: '',
          address: '',
          latidute: '',
          longidute: '',
          contact: '',
          phoneNo: ''
        },
        dataRule: {
          site: [
            { required: true, message: '站点不能为空', trigger: 'blur' }
          ],
          city: [
            { required: true, message: '城市不能为空', trigger: 'blur' }
          ],
          district: [
            { required: true, message: '区域不能为空', trigger: 'blur' }
          ],
          // superiorSite: [
          //   { required: true, message: '国家站点不能为空', trigger: 'blur' }
          // ],
          address: [
            { required: true, message: '地址不能为空', trigger: 'blur' }
          ],
          latidute: [
            { required: true, message: '经度不能为空', trigger: 'blur' }
          ],
          longidute: [
            { required: true, message: '纬度不能为空', trigger: 'blur' }
          ],
          contact: [
            { required: true, message: '联系人不能为空', trigger: 'blur' }
          ],
          phoneNo: [
            { required: true, message: '联系电话不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/site/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.site = data.site.site
                this.dataForm.city = data.site.city
                this.dataForm.district = data.site.district
                // this.dataForm.superiorSite = data.site.superiorSite
                this.dataForm.address = data.site.address
                this.dataForm.latidute = data.site.latidute
                this.dataForm.longidute = data.site.longidute
                this.dataForm.contact = data.site.contact
                this.dataForm.phoneNo = data.site.phoneNo
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
              url: this.$http.adornUrl(`/generator/site/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'site': this.dataForm.site,
                'city': this.dataForm.city,
                'district': this.dataForm.district,
                // 'superiorSite': this.dataForm.superiorSite,
                'address': this.dataForm.address,
                'latidute': this.dataForm.latidute,
                'longidute': this.dataForm.longidute,
                'contact': this.dataForm.contact,
                'phoneNo': this.dataForm.phoneNo
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
