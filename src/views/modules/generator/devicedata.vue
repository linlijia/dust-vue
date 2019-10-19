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
        <el-button v-if="isAuth('generator:devicedata:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('generator:devicedata:delete')" type="danger" @click="deleteHandle()"
                   :disabled="dataListSelections.length <= 0">批量删除
        </el-button>
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
        prop="dataTime"
        header-align="center"
        align="center"
        label="数据时间">
      </el-table-column>
      <el-table-column
        prop="siteName"
        header-align="center"
        align="center"
        label="监测站点">
      </el-table-column>
      <el-table-column
        prop="mn"
        header-align="center"
        align="center"
        label="设备编号">
      </el-table-column>
      <el-table-column
        prop="a34011Rtd"
        header-align="center"
        align="center"
        label="月度称量增重（g）">
      </el-table-column>
      <el-table-column
        prop="a34011Ori"
        header-align="center"
        align="center"
        label="初重（g）">
      </el-table-column>
      <el-table-column
        prop="dustData"
        header-align="center"
        align="center"
        label="月降尘量（t/km²*M）">
      </el-table-column>
      <el-table-column
        prop="weight"
        header-align="center"
        align="center"
        label="内校砝码值（g）">
      </el-table-column>
      <el-table-column
        prop="a34011Flag"
        header-align="center"
        align="center"
        :formatter="(row, column, cellValue)=>{return sMap[cellValue]}"
        label="降尘数据标识位">
      </el-table-column>
      <el-table-column
        prop="a34011Day"
        header-align="center"
        align="center"
        label="有效天数">
      </el-table-column>
      <!-- <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column> -->
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
  import AddOrUpdate from './devicedata-add-or-update'
  import sMap from '@/utils/statusMap.js'

  const searchList = [{'value': 'mn', 'label': '按MN码'}, {'value': 'site_name', 'label': '按站点'}]
  export default {
    data() {
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
        sMap: sMap,
        siteName: '',
        a34011Day: 0,
        dustData: '',
        searchList: searchList
      }
    },
    components: {
      AddOrUpdate
    },
    activated() {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/devicedata/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'sidx': 'dataTime',
            'order': 'DESC',
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
      sizeChangeHandle(val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle(val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle(val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle(id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle(id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/devicedata/delete'),
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
