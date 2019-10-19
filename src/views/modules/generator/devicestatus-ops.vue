<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-select v-model="dataForm.key">
          <el-option v-for="(i, idx) in searchList" :key="idx" :value="i.value" :label="i.label"/>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.value" placeholder="搜索" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('generator:devicestatus:delete')" type="danger" @click="deleteHandle()"
                   :disabled="dataListSelections.length <= 0">批量删除
        </el-button>
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.cmd">
          <el-option v-for="(i, idx) in cmdList" :key="idx" :value="i" :label="i.label"/>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="executeHandle()">批量执行</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        fixed="left"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="siteName"
        fixed="left"
        header-align="center"
        align="center"
        label="站点名称">
      </el-table-column>
      <el-table-column
        prop="mn"
        header-align="center"
        align="center"
        fixed="left"
        label="设备编码">
      </el-table-column>
      <el-table-column
        prop="dataTime"
        header-align="center"
        align="center"
        fixed="left"
        label="数据时间">
      </el-table-column>

      <el-table-column
        prop="currentStatus"
        header-align="center"
        align="center"
        :formatter="formatStatus"
        label="设备状态">
      </el-table-column>
      <el-table-column
        prop="operation"
        header-align="center"
        align="center"
        :formatter="formatOperation"
        label="当前操作">
      </el-table-column>
      <el-table-column
        prop="troubleSet"
        header-align="center"
        align="center"
        :formatter="formatTrouble"
        label="故障情况">
      </el-table-column>
      <el-table-column
        prop="weight"
        header-align="center"
        align="center"
        label="重量">
      </el-table-column>
      <el-table-column
        prop="doorStatus"
        header-align="center"
        align="center"
        :formatter="formatDoorStatus"
        label="门状态">
      </el-table-column>
      <el-table-column
        prop="trayStatus"
        header-align="center"
        align="center"
        :formatter="formatTrayStatus"
        label="托盘状态">
      </el-table-column>
      <el-table-column
        prop="rainfall"
        header-align="center"
        align="center"
        label="降雨量">
      </el-table-column>
      <el-table-column
        prop="outTemperature"
        header-align="center"
        align="center"
        label="环境温度">
      </el-table-column>
      <el-table-column
        prop="outHumidity"
        header-align="center"
        align="center"
        label="环境湿度">
      </el-table-column>
      <el-table-column
        prop="entranceTemperature"
        header-align="center"
        align="center"
        label="入口温度">
      </el-table-column>
      <el-table-column
        prop="entranceHumidity"
        header-align="center"
        align="center"
        label="入口湿度">
      </el-table-column>
      <el-table-column
        prop="exitTemperature"
        header-align="center"
        align="center"
        label="出口温度">
      </el-table-column>
      <el-table-column
        prop="exitHumidity"
        header-align="center"
        align="center"
        label="出口湿度">
      </el-table-column>
      <el-table-column
        prop="plateTemperature1"
        header-align="center"
        align="center"
        label="热盘温度1">
      </el-table-column>
      <el-table-column
        prop="plateTemperature2"
        header-align="center"
        align="center"
        label="热盘温度2">
      </el-table-column>
      <el-table-column
        prop="plateTemperature3"
        header-align="center"
        align="center"
        label="热盘温度3">
      </el-table-column>
      <el-table-column
        prop="airTemperature"
        header-align="center"
        align="center"
        label="温控温度">
      </el-table-column>
      <el-table-column
        prop="waterTemperature"
        header-align="center"
        align="center"
        label="水蒸气水温">
      </el-table-column>
      <el-table-column
        prop="trayPosition11"
        header-align="center"
        align="center"
        label="电位器11">
      </el-table-column>
      <el-table-column
        prop="trayPosition12"
        header-align="center"
        align="center"
        label="电位器12">
      </el-table-column>
      <el-table-column
        prop="trayPosition21"
        header-align="center"
        align="center"
        label="电位器21">
      </el-table-column>
      <el-table-column
        prop="trayPosition22"
        header-align="center"
        align="center"
        label="电位器22">
      </el-table-column>
      <el-table-column
        prop="up1Limit"
        header-align="center"
        align="center"
        label="托盘上限位1">
      </el-table-column>
      <el-table-column
        prop="up2Limit"
        header-align="center"
        align="center"
        label="托盘上限位2">
      </el-table-column>
      <el-table-column
        prop="down1Limit"
        header-align="center"
        align="center"
        label="托盘下限位1">
      </el-table-column>
      <el-table-column
        prop="down2Limit"
        header-align="center"
        align="center"
        label="托盘下限位2">
      </el-table-column>
      <el-table-column
        prop="openLimit"
        header-align="center"
        align="center"
        label="开门限位">
      </el-table-column>
      <el-table-column
        prop="closeLimit"
        header-align="center"
        align="center"
        label="关门限位">
      </el-table-column>
      <el-table-column
        prop="bucketTrigger"
        header-align="center"
        align="center"
        label="水箱水位">
      </el-table-column>
      <el-table-column
        prop="steamTrigger"
        header-align="center"
        align="center"
        label="水蒸汽水位">
      </el-table-column>
      <el-table-column
        prop="balanceFlag"
        header-align="center"
        align="center"
        label="校准结果">
      </el-table-column>
      <el-table-column
        prop="balanceMode"
        header-align="center"
        align="center"
        label="称量状态">
      </el-table-column>
      <el-table-column
        prop="printMode"
        header-align="center"
        align="center"
        label="数据有效性">
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './devicestatus-add-or-update'

  const searchList = [{'value': 'mn', 'label': '按MN码'}, {'value': 'site_name', 'label': '按站点'}]

  export default {
    data () {
      return {
        dataForm: {
          key: searchList[0].value,
          value: '',
          cmd: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        siteName: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        searchList: searchList,
        cmdList: []
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    mounted () {
      this.getCmdList()
      if (this.timer) {
        clearInterval(this.timer)
      } else {
        this.timer = setInterval(() => {
          this.getDataList()
        }, 20000)
      }
    },
    destroyed () {
      clearInterval(this.timer)
    },
    methods: {
      // 获取命令列表
      getCmdList () {
        this.$http({
          url: this.$http.adornUrl('/generator/device/command/list'),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.cmdList = data.data
          }
        })
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/devicestatus/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'searchType': 'like',
            ...this.dataForm
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
    // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
    // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
    // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      executeHandle (mn) {
        var mns = mn ? [mn] : this.dataListSelections.map(item => {
          return item.mn
        })
        var cmd = this.dataForm.cmd
        this.$confirm(`确定对mn=[${mns.join(',')}]进行[${cmd.label}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/device/command/execute'),
            method: 'post',
            data: this.$http.adornData({
              'mns': mns,
              'code': cmd.value
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
      formatStatus (row, column) {
        switch (row.currentStatus) {
          case 1000:
            return '待机'
          case 2001:
            return '开门中'
          case 2002:
            return '关门中'
          case 2003:
            return '托盘上升中'
          case 2004:
            return '托盘下降中'
          case 2005:
            return '托盘前往平衡位中'
          case 2006:
            return '烘烤中'
          case 2007:
            return '制冷中'
          case 2008:
            return '静置中'
          case 2009:
            return '机芯校准中'
          case 2010:
            return '机芯清零中'
          case 2011:
            return '称重中'
          case 2012:
            return '拍照中'
          case 2013:
            return '测试称量中'
          case 9999:
            return '未知'
        }
      },
      formatOperation (row, column) {
        switch (row.operation) {
          case 111:
            return '拍照开始'
          case 112:
            return '发送拍照请求'
          case 113:
            return '保存图片'
          case 114:
            return '上传图片'
          case 115:
            return '拍照结束'
          case 121:
            return '生成抓图失败故障'
          case 211:
            return '称量开始'
          case 212:
            return '静置'
          case 213:
            return '称重'
          case 311:
            return '关门'
          case 312:
            return '开门'
          case 313:
            return '托盘上升'
          case 314:
            return '托盘下降'
          case 315:
            return '托盘前往平衡位'
          case 316:
            return '烘烤'
          case 317:
            return '制冷平衡'
          case 318:
            return '重新校准机芯'
          case 319:
            return '关机芯'
          case 320:
            return '机芯清零'
          case 401:
            return '测试称量开始'
          case 911:
            return '生成开门故障'
          case 912:
            return '生成托盘上升故障'
          case 913:
            return '生成加热故障'
          case 914:
            return '生成关门故障'
          case 915:
            return '生成制冷故障'
          case 916:
            return '生成机芯校准故障'
          case 917:
            return '生成机芯清零故障'
          case 918:
            return '生成负增长故障'
          case 999:
            return '无操作'
        }
      },
      formatTrouble (row, column) {
        if (row.troubleSet === '90000') {
          return '无故障'
        } else {
          if (row.troubleSet !== null) {
            return row.troubleSet
          } else {
            return '--'
          }
        }
      },
      formatDoorStatus (row, column) {
        switch (row.doorStatus) {
          case 0:
            return '关'
          case 1:
            return '开'
          case 2:
            return '未知'
          case 3:
            return '异常'
        }
      },
      formatTrayStatus (row, column) {
        switch (row.trayStatus) {
          case 1:
            return '集尘'
          case 2:
            return '平衡'
          case 3:
            return '称量'
          case 4:
            return '未知'
          case 5:
            return '异常'
          default:
            return '--'
        }
      }
    }
  }
  // const timer = setInterval(() => {
  //   // 某些定时器操作
  //   console.log('xxx')
  // }, 2000)
  //
  // this.$once('hook:beforeDestroy', () => {
  //   clearInterval(timer)
  // })
</script>
