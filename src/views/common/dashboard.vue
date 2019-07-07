<template>
  <div class="mod-dashboard">
    <el-row :gutter="10">

      <el-col :span="8">

        <el-col class="num" :span="24">
          <p class="tot">总设备数：{{num.total || 0}}</p>
          <p class="off">离线设备数：{{num.offline || 0}}</p>
          <p class="brk">故障设备数：{{num.failure || 0}}</p>
        </el-col>
        <el-col class="" :span="24">
          <el-table
            :data="tableList"
            border
            v-loading="tableListLoading"
            class="home-table"
            style="width: 100%;"
            max-height="295">
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
        </el-col>
      </el-col>
      <el-col :span="16">
        <HomeMap :propStyle="propStyle" :num="num"></HomeMap>
      </el-col>

      <el-col :span="24">
        <div id="J_dashBarBox" class="chart-box"></div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import HomeMap from '@/components/home-map'
  import echarts from 'echarts'

  export default {
    data() {
      return {
        num: {
          failure: 0,
          offline: 0,
          online: 0,
          total: 0
        },
        propStyle: 'height: 400px',
        tableList: [],
        tableListLoading: false,

        chartData: [],
        chartBar: null
      }
    },
    components: {
      HomeMap
    },
    methods: {
      getNumData() {
        this.$http({
          url: this.$http.adornUrl('/generator/device/status')
        }).then(({data}) => {
          if (data.count) {
            this.num = data.count
          }
        })
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
            // name: '月度称量增重（单位：g）'
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
      },
    },
    mounted() {
      this.getNumData()
      this.$http({
        url: this.$http.adornUrl('/generator/trouble/list'),
        method: 'get',
        params: this.$http.adornParams({
          'sidx': 'happen_time',
          'order': 'desc'
        })
      }).then(({data}) => {
        if (data.page) {
          this.tableList = data.page.list || []
        }
      })

      this.getChartData()
    },
  }
</script>

<style lang="scss">
  .mod-dashboard {
    .num {
      p {
        line-height: 30px;
        margin-bottom: 5px;
        color: #fff;
        text-align: center
      }
      .tot {
        background: #0ACD80;
      }
      .off {
        background: #8b8787
      }
      .brk {
        background: #FB0505
      }
    }

    .chart-box {
      min-height: 500px;
      margin-top: 15px;
    }
  }
</style>

