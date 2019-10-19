<template>
  <div class="mod-dashboard">
    <el-row :gutter="10">

      <el-col :span="8">

        <el-col class="num" :span="24">
          <devStatistics></devStatistics>
        </el-col>
        <el-col class="" :span="24">
          <troubleMessageList></troubleMessageList>
        </el-col>
      </el-col>
      <el-col :span="16">
        <el-card :body-style="{padding: '0px'}" shadow="always">
          <HomeMap :propStyle="propStyle" :num="num"></HomeMap>
        </el-card>
      </el-col>

      <el-col :span="24">
        <weightChart :style="chartStyle"></weightChart>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import HomeMap from '@/components/home-map'
import devStatistics from './dev-statistics'
import troubleMessageList from './trouble-message-list'
import weightChart from './weight-chart'

export default {
  data () {
    return {
      num: {
        failure: 0,
        offline: 0,
        online: 0,
        total: 0
      },
      propStyle: 'height: 595px',
      chartStyle: 'margin: 20px 0 0 5px',
      chartData: [],
      chartBar: null
    }
  },
  components: {
    HomeMap,
    devStatistics,
    troubleMessageList,
    weightChart
  },
  methods: {
    getNumData () {
      this.$http({
        url: this.$http.adornUrl('/generator/device/status')
      }).then(({data}) => {
        if (data.count) {
          this.num = data.count
        }
      })
    }
  },
  mounted () {
    this.getNumData()
  }
}
</script>

<style lang="scss">
  .num {
    margin-bottom: 12px
  }
</style>

