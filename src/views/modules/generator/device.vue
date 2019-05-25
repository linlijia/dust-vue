<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-select v-model="dataForm.key">
          <el-option v-for="(i, idx) in searchList" :key="idx" :value="i.value" :label="i.label" />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.value" placeholder="搜索" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('generator:device:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <!-- <el-button v-if="isAuth('generator:device:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button> -->
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
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        type="index"
        :index="(index)=>{return (pageIndex-1)*pageSize + index +1}"
        header-align="center"
        align="center"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="site"
        header-align="center"
        align="center"
        label="站点">
      </el-table-column>
      <el-table-column
        prop="mn"
        header-align="center"
        align="center"
        label="MN码">
      </el-table-column>
      <el-table-column
        prop="interfacePath"
        header-align="center"
        align="center"
        label="接口根路径">
      </el-table-column>
      <el-table-column
        prop="serviceInfo"
        header-align="center"
        align="center"
        label="服务端信息">
      </el-table-column>
      <el-table-column
        prop="passwd"
        header-align="center"
        align="center"
        label="服务端密码">
      </el-table-column>
      <el-table-column
        prop="systemType"
        header-align="center"
        align="center"
        label="系统类型">
      </el-table-column>
      <el-table-column
        prop="serialPortType"
        header-align="center"
        align="center"
        label="串口名">
      </el-table-column>
      <el-table-column
        prop="temperatureHumidityLimit"
        header-align="center"
        align="center"
        label="温度湿度限制">
      </el-table-column>
      <el-table-column
        prop="uploadInterval"
        header-align="center"
        align="center"
        label="称量间隔（小时）">
      </el-table-column>
      <el-table-column
        prop="taskHour"
        header-align="center"
        align="center"
        label="启动任务小时">
      </el-table-column>
      <el-table-column
        prop="taskMinute"
        header-align="center"
        align="center"
        label="启动任务分钟">
      </el-table-column>
      <el-table-column
        prop="createAt"
        header-align="center"
        align="center"
        label="添加时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <!-- <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button> -->
          <el-button type="text" size="small" @click="detailClick(scope.row.id)">查看详情</el-button>
        </template>
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
    <device-detail v-if="deviceDetaileVisible" ref="deviceDetail"></device-detail>
  </div>
</template>

<script>
  import AddOrUpdate from './device-add-or-update'
  import DeviceDetail from './device-detail'
  const searchList = [{'value':'mn', 'label':'按MN码'}, {'value':'site', 'label':'按站点'}]
  export default {
    data () {
      return {
        dataForm: {
          key: searchList[0].value,
          value: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        deviceDetaileVisible: false,
        searchList: searchList
      }
    },
    components: {
      AddOrUpdate,
      DeviceDetail
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/device/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'searchType' : 'like',
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
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/device/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
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
      // detail
      detailClick (id) {
        this.deviceDetaileVisible = true
        this.$nextTick(() => {
          this.$refs.deviceDetail.init(id)
        })
      }
    }
  }
</script>
