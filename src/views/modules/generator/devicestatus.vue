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
        <el-button v-if="isAuth('generator:devicestatus:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('generator:devicestatus:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        prop="s10001"
        header-align="center"
        align="center"
        label="X轴偏移量">
      </el-table-column>
      <el-table-column
        prop="s10002"
        header-align="center"
        align="center"
        label="Y轴偏移量">
      </el-table-column>
      <el-table-column
        prop="s10003"
        header-align="center"
        align="center"
        label="热盘温度">
      </el-table-column>
      <el-table-column
        prop="s10004"
        header-align="center"
        align="center"
        label="降尘室温度">
      </el-table-column>
      <el-table-column
        prop="s10005"
        header-align="center"
        align="center"
        label="降尘室温度">
      </el-table-column>
      <el-table-column
        prop="s10006"
        header-align="center"
        align="center"
        label="测量室温度">
      </el-table-column>
      <el-table-column
        prop="s10007"
        header-align="center"
        align="center"
        label="测量室湿度">
      </el-table-column>
      <el-table-column
        prop="s10008"
        header-align="center"
        align="center"
        label="电源电压">
      </el-table-column>
      <el-table-column
        prop="s10009"
        header-align="center"
        align="center"
        label="电池电压">
      </el-table-column>
      <el-table-column
        prop="s10010"
        header-align="center"
        align="center"
        label="经度">
      </el-table-column>
      <el-table-column
        prop="s10011"
        header-align="center"
        align="center"
        label="纬度">
      </el-table-column>
      <el-table-column
        prop="s10012"
        header-align="center"
        align="center"
        label="高度">
      </el-table-column>
      <el-table-column
        prop="s10013"
        header-align="center"
        align="center"
        label="门状态">
      </el-table-column>
      <el-table-column
        prop="s10014"
        header-align="center"
        align="center"
        label="托盘状态">
      </el-table-column>
      <el-table-column
        prop="s10015"
        header-align="center"
        align="center"
        label="降水状态">
      </el-table-column>
      <el-table-column
        prop="s10016"
        header-align="center"
        align="center"
        label="水箱水位状态">
      </el-table-column>
      <el-table-column
        prop="s10017"
        header-align="center"
        align="center"
        label="机芯上电状态">
      </el-table-column>
      <el-table-column
        prop="s10018"
        header-align="center"
        align="center"
        label="机芯校准状态">
      </el-table-column>
      <el-table-column
        prop="operation"
        header-align="center"
        align="center"
        label="操作">
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
  const searchList = [{'value':'mn', 'label':'按MN码'},{'value':'site_name', 'label':'按站点'}]
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
        siteName: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        searchList: searchList
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/devicestatus/list'),
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
            url: this.$http.adornUrl('/generator/devicestatus/delete'),
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
      }
    }
  }
</script>
