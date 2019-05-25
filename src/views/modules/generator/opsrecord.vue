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
        <!--<el-button v-if="isAuth('generator:opsrecord:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>-->
        <el-button v-if="isAuth('generator:opsrecord:delete')" type="danger" @click="deleteHandle()"
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
        header-align="center"
        width="50"
        align="center"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="opsTime"
        header-align="center"
        align="center"
        label="运维日期">
      </el-table-column>
      <el-table-column
        prop="siteName"
        header-align="center"
        align="center"
        label="站点">
      </el-table-column>
      <el-table-column
        prop="opsUserName"
        header-align="center"
        align="center"
        label="运维人员">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="movementLevel"
        header-align="center"
        align="center"
        label="机芯水平">
      </el-table-column>
      <el-table-column
        prop="movementLevelNote"
        header-align="center"
        align="center"
        label="机芯水平备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="filterReplacement"
        header-align="center"
        align="center"
        label="滤膜更换">
      </el-table-column>
      <el-table-column
        prop="filterReplacementNote"
        header-align="center"
        align="center"
        label="滤膜更换备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="weighingDust"
        header-align="center"
        align="center"
        label="称量仓除尘">
      </el-table-column>
      <el-table-column
        prop="weighingDustNote"
        header-align="center"
        align="center"
        label="称量仓除尘备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="externalDust"
        header-align="center"
        align="center"
        label="外部除尘">
      </el-table-column>
      <el-table-column
        prop="externalDustNote"
        header-align="center"
        align="center"
        label="外部除尘备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="circulatorySystemDust"
        header-align="center"
        align="center"
        label="循环系统除尘">
      </el-table-column>
      <el-table-column
        prop="circulatorySystemDustNote"
        header-align="center"
        align="center"
        label="循环系统除尘备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="waterStorage"
        header-align="center"
        align="center"
        label="储水桶补水">
      </el-table-column>
      <el-table-column
        prop="waterStorageNote"
        header-align="center"
        align="center"
        label="储水桶补水备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="componentReplacement"
        header-align="center"
        align="center"
        label="部件替换">
      </el-table-column>
      <el-table-column
        prop="componentReplacementNote"
        header-align="center"
        align="center"
        label="部件替换备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="equipmentAppearanceCheck"
        header-align="center"
        align="center"
        label="设备外观检查">
      </el-table-column>
      <el-table-column
        prop="equipmentAppearanceCheckNote"
        header-align="center"
        align="center"
        label="设备外观检查备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="deviceStatus"
        header-align="center"
        align="center"
        label="离场时设备运行状态">
      </el-table-column>
      <el-table-column
        prop="deviceStatusNote"
        header-align="center"
        align="center"
        label="离场使设备运行状态备注">
      </el-table-column>
      <el-table-column
        :formatter="formatCheckBox"
        prop="opsRecord"
        header-align="center"
        align="center"
        label="监测点运维记录单">
      </el-table-column>
      <el-table-column
        prop="opsRecordNote"
        header-align="center"
        align="center"
        label="监测点运维记录单备注">
      </el-table-column>
      <!--<el-table-column-->
      <!--prop="createAt"-->
      <!--header-align="center"-->
      <!--align="center"-->
      <!--label="">-->
      <!--</el-table-column>-->
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
  import AddOrUpdate from './opsrecord-add-or-update'

  const searchList = [{'value': 'site_name', 'label': '按站点'}, {'value': 'ops_user_name', 'label': '运维人员'}]
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
      formatCheckBox: function (row, column, cellValue) {
        if (column.property == 'filterReplacement') {
          return cellValue == 0 ? '未更换' : cellValue == 1 ? '更换' : '未知'
        }
        if (column.property == 'movementLevel') {
          return cellValue == 0 ? '调整' : cellValue == 1 ? '正常' : '未知'
        }
        return cellValue == 0 ? '未完成' : cellValue == 1 ? '完成' : '未知';
      },
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/opsrecord/list'),
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
          var source = 'opsRecord';
          this.$refs.addOrUpdate.init(id, source)
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
            url: this.$http.adornUrl('/generator/opsrecord/delete'),
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


    }
  }
</script>
