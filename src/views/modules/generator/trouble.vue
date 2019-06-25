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
        <!--<el-button v-if="isAuth('generator:trouble:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>-->
        <el-button v-if="isAuth('generator:trouble:delete')" type="danger" @click="deleteHandle()"
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
        type="index"
        :index="(index)=>{return (pageIndex-1)*pageSize + index +1}"
        header-align="center"
        align="center"
        label="序号">
      </el-table-column>
      <!--<el-table-column-->
      <!--prop="id"-->
      <!--header-align="center"-->
      <!--align="center"-->
      <!--label="编号">-->
      <!--</el-table-column>-->
      <el-table-column
        prop="happenTime"
        header-align="center"
        align="center"
        label="发生时间">
      </el-table-column>
      <el-table-column
        prop="siteName"
        header-align="center"
        align="center"
        label="站点名称">
      </el-table-column>
      <el-table-column
        prop="mn"
        header-align="center"
        align="center"
        label="设备编码">
      </el-table-column>
      <el-table-column
        prop="troubleCode"
        header-align="center"
        align="center"
        :disable=true
        label="故障类型编码">
      </el-table-column>
      <el-table-column
        prop="troublCodeName"
        header-align="center"
        align="center"
        label="故障类型">
      </el-table-column>
      troubleCodeList
      <el-table-column
        prop="troubleDescription"
        header-align="center"
        align="center"
        label="故障描述">
      </el-table-column>
      <el-table-column
        prop="solved"
        header-align="center"
        align="center"
        :formatter="(row)=>{return row.solved == 0?'未解决' : row.solved ==1?'已解决' :'--'}"
        label="故障处理状态">
      </el-table-column>
      <el-table-column
        prop="solvedTime"
        header-align="center"
        align="center"
        label="解决时间">
      </el-table-column>
      <el-table-column
        prop="solvedMethod"
        header-align="center"
        align="center"
        :formatter="(row)=>{return row.solvedMethod == 0?'设备自修复' : row.solvedMethod ==1?'远程处理' : row.solvedMethod==2?'现场处理':'--'}"
        label="处理方式">
      </el-table-column>
      <el-table-column
        prop="userName"
        header-align="center"
        align="center"
        label="故障解决人员">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
  </div>
</template>

<script>
  import AddOrUpdate from './trouble-add-or-update'

  const searchList = [{'value': 'site_name', 'label': '按站点'}, {'value': 'name', 'label': '按姓名'}]
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
        troublCodeName: '',
        searchList: searchList
      }
    },
    troubleCodeList: [],
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
          url: this.$http.adornUrl('/generator/trouble/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'sidx': 'id',
            'order': 'desc',
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
            url: this.$http.adornUrl('/generator/trouble/delete'),
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
      }
    }
  }
</script>
