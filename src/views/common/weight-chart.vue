<template>
  <el-tabs v-model="activeName" type="border-card" @tab-click="handleClick">
    <el-tab-pane label="实时" name="realTime">
      <div id="J_dashBarBox" class="chart-box"></div>
    </el-tab-pane>
    <el-tab-pane label="上个月" name="lastMonth">
      <div id="J_dashBarBox2" class="chart-box"></div>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
import echarts from 'echarts'

export default {
  data () {
    return {
      activeName: 'realTime'
    }
  },
  mounted () {
    this.getChartData()
  },
  methods: {
    handleClick (tab, event) {
      console.log(tab, event)
    },
    getChartData() {
      this.$http({
        url: this.$http.adornUrl('/generator/devicedata/monthly'),
        params: {
          month: `${new Date().getFullYear()}-${new Date().getMonth() + 1}`
        }
      }).then((res) => {
        if (res.data) {

          this.initChart1(res.data.data)
        }
      })
    },
    initChart1(data = {}) {
      var dustData = data.dustData || {}
      var avgDate = data.dustDataAvg || '0.00'
      // var chartLegend = []
      var chartxAxis = []
      var chartSeries = []
      for (let [key, value] of Object.entries(dustData)) {
        chartxAxis.push(key)
        chartSeries.push(value)
      }

      var option = {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: {
          data: chartxAxis,
          axisLabel: {
            rotate: 60
          },
        },
        legend: {
          data: ['降尘自动监测仪测量值', '降尘自动监测仪平均值']
        },
        yAxis: {
          type: 'value',
          name: '月度称量增重（单位：g）'
        },
        series: [{
          name: '降尘自动监测仪测量值',
          type: 'bar',
          data: chartSeries
        }, {
          name: '降尘自动监测仪平均值',
          type: 'line',
          data: chartSeries.map(i => avgDate)
        }]
      }
      this.chartBar1 = echarts.init(document.getElementById('J_dashBarBox'))
      this.chartBar1.setOption(option)
      window.addEventListener('resize', () => {
        this.chartBar1.resize()
      })
    }
  }
}
</script>

<style scoped>
  .chart-box {
    min-height: 500px;
    margin-top: 15px;
  }
</style>
