<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-select v-model="dataForm.key" placeholder="任务状态" @change="keyChange">
          <el-option v-for="(i, idx) in searchList" :key="idx" :value="i.value" :label="i.label"/>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-select v-if="showType=='opsStatus'" v-model="dataForm.value" placeholder="任务状态">
          <el-option v-for="(i, idx) in statusMap" :key="idx" :value="i.value" :label="i.label"/>
        </el-select>
        <el-date-picker
          v-if="showType=='opsDate'"
          v-model="dataForm.value"
          type="date"
          placeholder="选择执行日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('generator:opstask:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <!-- <el-button v-if="isAuth('generator:opstask:delete')" type="danger" @click="deleteHandle()"
                   :disabled="dataListSelections.length <= 0">批量删除
        </el-button> -->
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
      <!--<el-table-column-->
      <!--prop="id"-->
      <!--header-align="center"-->
      <!--align="center"-->
      <!--hidden-->
      <!--label="任务ID">-->
      <!--</el-table-column>-->
      <el-table-column
        prop="opsDate"
        header-align="center"
        align="center"
        label="执行日期">
      </el-table-column>

      <el-table-column
        prop="siteName"
        header-align="center"
        align="center"
        label="站点">
      </el-table-column>
      <el-table-column
        prop="taskType"
        header-align="center"
        align="center"
        label="任务类型">
      </el-table-column>
      <el-table-column
        prop="assignerName"
        header-align="center"
        align="center"
        label="任务分配人">
      </el-table-column>
      <el-table-column
        prop="opsUserName"
        header-align="center"
        align="center"
        label="运维人员">
      </el-table-column>
      <el-table-column
        prop="note"
        header-align="center"
        align="center"
        label="任务简介">
      </el-table-column>
      <el-table-column
        prop="opsStatus"
        header-align="center"
        align="center"
        label="任务状态">
      </el-table-column>
      <el-table-column
        prop="startTime"
        header-align="center"
        align="center"
        :formatter="(row)=>{return row.startTime || '--'}"
        label="运维起始时间">
      </el-table-column>
      <el-table-column
        prop="endTime"
        header-align="center"
        align="center"
        :formatter="(row)=>{return row.endTime || '--'}"
        label="运维截止时间">
      </el-table-column>
      <el-table-column
        prop="travelMode"
        header-align="center"
        align="center"
        label="出行方式">
      </el-table-column>
      <el-table-column
        prop="checkIn"
        header-align="center"
        align="center"
        label="签到时间">
      </el-table-column>
      <el-table-column
        prop="createAt"
        header-align="center"
        align="center"
        label="新建任务时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="signInHandle(scope.row.id)">签到</el-button>
          <el-button type="text" size="small" @click="addOrUpdateHandle2(scope.row.id)">操作</el-button>
          <el-button v-if="scope.row.opsStatus=='待执行'" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">编辑</el-button>
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
    <add-or-update2 v-if="addOrUpdateVisible2" ref="addOrUpdate2" @refreshDataList="getDataList"></add-or-update2>
  </div>
</template>

<script>
  import AddOrUpdate from './opstask-add-or-update'
  import AddOrUpdate2 from './opsrecord-add-or-update'

  const searchList = [{'value': 'opsStatus', 'label': '按任务状态'}, {'value': 'opsDate', 'label': '按任执行日期'}]

  export default {
    data() {
      return {
        dataForm: {
          key: searchList[0].value,
          value: ''
        },
        showType: searchList[0].value,
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,

        addOrUpdateVisible2: false,
        searchList: searchList,
        statusMap: [{'value': '', 'label': '全部'}, {'value': '待执行', 'label': '待执行'}, {
          'value': '正在执行',
          'label': '正在执行'
        }, {
          'value': '已执行',
          'label': '已执行'
        }]
      }
    },
    components: {
      AddOrUpdate,
      AddOrUpdate2
    },
    activated() {
      this.getDataList()
    },
    methods: {
      keyChange(val) {
        this.dataForm.value = ''
        this.showType = val
      },
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/opstask/list'),
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
          this.$refs.addOrUpdate.init(id)
        })
      },
      addOrUpdateHandle2(id) {
        this.addOrUpdateVisible2 = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate2.init(id)
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
            url: this.$http.adornUrl('/generator/opstask/delete'),
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
      // 签到
      signInHandle(id) {
        const now = new Date()
        const checkInTime = now.getFullYear() + '-' + now.getMonth() + '-' + now.getDate() + ' ' +
          now.getHours() + ':' + now.getMinutes() + ':' + now.getSeconds()

        this.$http({
          url: this.$http.adornUrl(`/generator/opstask/checkIn`),
          method: 'post',
          data: this.$http.adornData({
            'id': id,
            'checkIn': checkInTime
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
      }
    }
  }
</script>
