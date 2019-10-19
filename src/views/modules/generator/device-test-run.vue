<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-select v-model="selectedStatus.key">
          <el-option v-for="(i, index) in statusList" :key="index" :value="i.value" :label="i.label" />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.key">
          <el-option v-for="(i, idx) in searchList" :key="idx" :value="i.value" :label="i.label" />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.value" placeholder="搜索" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getWeightDataList()">查询</el-button>
      </el-form-item>
      <el-form-item style="float: right">
        <el-button type="primary" @click="exportExcel()">导出</el-button>
      </el-form-item>
    </el-form>
    <el-table
      class="weightDataTable"
      :data="weightDataList"
      border
      v-loading="weightDataListLoading"
      style="width: 100%;">
      <el-table-column
        prop="mn"
        header-align="center"
        align="center"
        fixed="left"
        label="设备编码">
      </el-table-column>
      <el-table-column
        prop="taskEndTime"
        header-align="center"
        align="center"
        fixed="left"
        label="称量时间">
      </el-table-column>
      <el-table-column
        prop="weight"
        header-align="center"
        align="center"
        label="称量值">
      </el-table-column>
      <el-table-column
        prop="weightWeight"
        header-align="center"
        align="center"
        label="砝码值">
      </el-table-column>
      <el-table-column
        prop="startInsideTemperature"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['仓内温度']),h('p', {}, ['(称量前)'])]}">
      </el-table-column>
      <el-table-column
        prop="startInsideHumidity"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['仓内湿度']),h('p', {}, ['(称量前)'])]}">
      </el-table-column>
      <el-table-column
        prop="endInsideTemperature"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['仓内温度']),h('p', {}, ['(称量后)'])]}">
      </el-table-column>
      <el-table-column
        prop="endInsideHumidity"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['仓内湿度']),h('p', {}, ['(称量后)'])]}">
      </el-table-column>
      <el-table-column
        prop="startOutsideTemperature"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['环境温度']),h('p', {}, ['(称量前)'])]}">
      </el-table-column>
      <el-table-column
        prop="startOutsideHumidity"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['环境湿度']),h('p', {}, ['(称量前)'])]}">
      </el-table-column>
      <el-table-column
        prop="endOutsideTemperature"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['环境温度']),h('p', {}, ['(称量后)'])]}">
      </el-table-column>
      <el-table-column
        prop="endOutsideHumidity"
        header-align="center"
        align="center"
        :render-header="(h)=>{return [h('p', {}, ['环境湿度']),h('p', {}, ['(称量后)'])]}">
      </el-table-column>
      <el-table-column
        prop="sampleDays"
        header-align="center"
        align="center"
        label="有效天数">
      </el-table-column>
      <el-table-column
        prop="taskStartTime"
        header-align="center"
        align="center"
        label="开始时间">
      </el-table-column>
      <el-table-column
        prop="taskEndTime"
        header-align="center"
        align="center"
        label="结束时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="50"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="detailClick(scope.row.id)">详情</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="weightSizeChangeHandle"
      @current-change="weightCurrentChangeHandle"
      :current-page="weightPageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="weightPageSize"
      :total="weightTotalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      style="margin-top: 12px; width: 100%;">
      <el-table-column
        prop="mn"
        header-align="center"
        align="center"
        fixed="left"
        :formatter="formatMn"
        label="设备编码">
      </el-table-column>
      <el-table-column
        prop="dataTime"
        header-align="center"
        align="center"
        fixed="left"
        :formatter="formatDataTime"
        label="数据时间">
      </el-table-column>
      <el-table-column
        prop="systemMode"
        header-align="center"
        align="center"
        :formatter="formatSystemMode"
        label="系统模式">
      </el-table-column>
      <el-table-column
        prop="realTimeWeight"
        header-align="center"
        align="center"
        label="实时重量">
      </el-table-column>
      <el-table-column
        prop="bucketWeight"
        header-align="center"
        align="center"
        label="称量值">
      </el-table-column>
      <el-table-column
        prop="weightWeight"
        header-align="center"
        align="center"
        label="砝码值">
      </el-table-column>
      <el-table-column
        prop="plateTemperature1"
        header-align="center"
        align="center"
        label="热盘温度1">
      </el-table-column>
      <el-table-column
        prop="measuringRoomTemperature"
        header-align="center"
        align="center"
        label="测量室温度">
      </el-table-column>
      <el-table-column
        prop="measuringRoomHumidity"
        header-align="center"
        align="center"
        label="测量室湿度">
      </el-table-column>
      <el-table-column
        prop="environmentTemperature"
        header-align="center"
        align="center"
        label="环境温度">
      </el-table-column>
      <el-table-column
        prop="environmentHumidity"
        header-align="center"
        align="center"
        label="环境湿度">
      </el-table-column>
      <el-table-column
        prop="potentiometer1"
        header-align="center"
        align="center"
        label="电位器1">
      </el-table-column>
      <el-table-column
        prop="potentiometer2"
        header-align="center"
        align="center"
        label="电位器2">
      </el-table-column>
      <el-table-column
        prop="hotPlateUpLimit"
        header-align="center"
        align="center"
        label="热盘上限位">
      </el-table-column>
      <el-table-column
        prop="hotPlateDownLimit"
        header-align="center"
        align="center"
        label="热盘下限位">
      </el-table-column>
      <el-table-column
        prop="rainfall"
        header-align="center"
        align="center"
        label="是否降雨">
      </el-table-column>
      <el-table-column
        prop="pitcherLowerLimit"
        header-align="center"
        align="center"
        label="水桶低水位">
      </el-table-column>
      <el-table-column
        prop="doorOpenLimit"
        header-align="center"
        align="center"
        label="开门限位">
      </el-table-column>
      <el-table-column
        prop="doorClosedLimit"
        header-align="center"
        align="center"
        label="关门限位">
      </el-table-column>
      <el-table-column
        prop="frontDoorPosition"
        header-align="center"
        align="center"
        label="前舱门位置">
      </el-table-column>
      <el-table-column
        prop="backDoorPosition"
        header-align="center"
        align="center"
        label="后舱门位置">
      </el-table-column>
      <el-table-column
        prop="tubuleUpLimit"
        header-align="center"
        align="center"
        label="导尘管上限位">
      </el-table-column>
      <el-table-column
        prop="tubuleDownLimit"
        header-align="center"
        align="center"
        label="导尘管下限位">
      </el-table-column>
      <el-table-column
        prop="plateTemperature2"
        header-align="center"
        align="center"
        label="热盘温度2">
      </el-table-column>
      <el-table-column
        prop="controlBoardTemperature"
        header-align="center"
        align="center"
        label="控制板温度">
      </el-table-column>
      <el-table-column
        prop="dataAvailable"
        header-align="center"
        align="center"
        label="数据有效性">
      </el-table-column>
      <el-table-column
        prop="operationFlag"
        header-align="center"
        align="center"
        label="动作标识">
      </el-table-column>
      <el-table-column
        prop="errorCode"
        header-align="center"
        align="center"
        label="错误码">
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
  </div>
</template>

<script>
import FileSaver from 'file-saver'
import XLSX from 'xlsx'
const statusList = [{'value': '0', 'label': '全部'}, {'value': '1', 'label': '在线'}, {'value': '2', 'label': '离线'},
  {'value': '3', 'label': '故障'}]
const searchList = [{'value': 'mn', 'label': '按MN码'}, {'value': 'site_name', 'label': '按站点'}]
export default {
  data () {
    return {
      selectedStatus: {
        key: statusList[0].value,
        value: ''
      },
      dataForm: {
        key: searchList[0].value,
        value: ''
      },
      weightDataList: [],
      weightPageIndex: 1,
      weightPageSize: 10,
      weightTotalPage: 0,
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      siteName: 0,
      weightDataListLoading: false,
      dataListLoading: false,
      searchList: searchList,
      statusList
    }
  },
  activated () {
    this.getWeightDataList()
    this.getDataList()
  },
  mounted () {
    if (this.timer) {
      clearInterval(this.timer)
    } else {
      this.timer = setInterval(() => {
        this.getDataList()
      }, 10000)
    }
  },
  methods: {
    getWeightDataList () {
      this.weightDataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/generator/devicedetail/dustfalldata/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.weightPageIndex,
          'limit': this.weightPageSize,
          'searchType': 'like',
          ...this.dataForm
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.weightDataList = data.page.list
          this.weightTotalPage = data.page.totalCount
        } else {
          this.weightDataListLoading = []
          this.weightTotalPage = 0
        }
        this.weightDataListLoading = false
      })
    },
    // 每页数
    weightSizeChangeHandle (val) {
      this.weightPageSize = val
      this.weightPageIndex = 1
      this.getWeightDataList()
    },
    // 当前页
    weightCurrentChangeHandle (val) {
      this.weightPageIndex = val
      this.getWeightDataList()
    },
    // 获取数据列表
    getDataList () {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/generator/devicestatus/list/ex'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'searchType': 'like',
          ...this.dataForm,
          ...this.selectedStatus
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
    exportExcel () {
      // 设置当前日期
      let time = new Date()
      let year = time.getFullYear()
      let month = time.getMonth() + 1
      let day = time.getDate()
      let name = year + '' + month + '' + day
      // console.log(name)
      /* generate workbook object from table */
      //  .table要导出的是哪一个表格
      var wb = XLSX.utils.table_to_book(document.querySelector('.weightDataTable'))
      /* get binary string as output */
      var wbout = XLSX.write(wb, {
        bookType: 'xlsx',
        bookSST: true,
        type: 'array'
      })
      try {
        //  name+'.xlsx'表示导出的excel表格名字
        FileSaver.saveAs(
          new Blob([wbout], { type: 'application/octet-stream' }),
          name + '.xlsx'
        )
      } catch (e) {
        if (typeof console !== 'undefined') console.log(e, wbout)
      }
      return wbout
    },
    detailClick (id) {
      this.$router.push({
        name: 'devicedetailtest',
        params: {id: id}
      })
    },
    formatMn (row, column) {
      return row.mn.substr(-4)
    },
    formatDataTime (row, column) {
      return row.dataTime.substr(-8)
    },
    formatSystemMode (row, column) {
      switch (row.systemMode) {
        case 35:
          return '集尘'
        case 36:
          return '干燥'
        case 37:
          return '平衡'
        case 38:
          return '称量'
        case 42:
          return '雨水保护'
      }
    }
  }
}
</script>

<style scoped>

</style>
