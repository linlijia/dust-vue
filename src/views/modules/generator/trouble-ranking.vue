<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-date-picker
          v-model="month"
          type="month"
          :default-value="new Date()"
          value-format="yyyy-MM"
          placeholder="选择月"
          @change="getDataList">
        </el-date-picker>
      </el-form-item>

    </el-form>
    <el-table
      :data="dataList"
      border
      style="width: 100%;">
      <el-table-column
        type="index"
        header-align="center"
        align="center"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="siteName"
        header-align="center"
        align="center"
        label="站点名称">
      </el-table-column>
      <el-table-column
        prop="breakdown"
        header-align="center"
        align="center"
        label="故障天数">
      </el-table-column>
      <el-table-column
        prop="offline"
        header-align="center"
        align="center"
        :disable=true
        label="离线天数">
      </el-table-column>
      <el-table-column
        prop="total"
        header-align="center"
        align="center"
        label="异常总天数">
      </el-table-column>

    </el-table>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        dataList: [],
        dataForm: {
          key: '',
          value: '',
          month: ''
        },
        month: `${new Date().getFullYear()}-${new Date().getMonth() + 1}`,
        offline: '',
        breakdown: ''
      }
    },
    activated() {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/generator/trouble/ranking'),
          method: 'get',
          params: this.$http.adornParams({
            month: this.month
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.data
          } else {
            this.dataList = []
          }
        })
      }
    }
  }
</script>
