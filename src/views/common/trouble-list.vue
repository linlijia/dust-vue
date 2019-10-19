<template>
  <el-table
    :data="troubleList"
    border
    v-loading="tableListLoading"
    class="home-table"
    style="width: 100%;"
    max-height="400">
    <el-table-column
      label="序号"
      type="index"
      header-align="center"
      align="center">
    </el-table-column>
    <el-table-column
      label="故障时间"
      prop="happenTime"
      header-align="center"
      align="center">
    </el-table-column>
    <el-table-column
      label="站点"
      prop="siteName"
      header-align="center"
      align="center">
    </el-table-column>
    <el-table-column
      label="故障类型"
      prop="troublCodeName"
      header-align="center"
      align="center">
    </el-table-column>
  </el-table>
</template>

<script>
export default {
  data () {
    return {
      troubleList: [],
      tableListLoading: false
    }
  },
  methods: {
    getTroubleList () {
      this.$http({
        url: this.$http.adornUrl('/generator/trouble/list'),
        method: 'get',
        params: this.$http.adornParams({
          'sidx': 'happen_time',
          'order': 'desc'
        })
      }).then(({data}) => {
        if (data.page) {
          this.troubleList = data.page.list || []
        }
      })
    }
  },
  mounted () {
    this.getTroubleList()
  }
}
</script>

<style scoped>

</style>
