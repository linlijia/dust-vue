<template>
  <div class="mod-tongji">
     <el-row :gutter="20">
       
      <el-col :span="24" class="mod-col">
        <!-- <el-card> -->
          <div class="flex-icon-bar">
            <h2>降尘自动监测仪排序图</h2>
          </div>
          <div class="tongji-filer">
              <el-date-picker
                v-model="value1"
                type="month"
                :default-value="new Date()"
                value-format="yyyy-MM"
                placeholder="选择月"
                @change="getChartData1">
              </el-date-picker>
            </div>
          <div id="J_chartBarBox1" class="chart-box"></div>
        <!-- </el-card> -->
      </el-col>

    </el-row>

  </div>
</template>

<script>
  import echarts from 'echarts'
  export default {
    data () {
      return {
        chartBar1: null,
        value1: `${new Date().getFullYear()}-${new Date().getMonth() + 1}`,
        chartType: 'line'
      }
    },
    mounted () {
      this.getChartData1()
    },
    methods: {
      getChartData1() {
        this.$http({
          url: this.$http.adornUrl('/generator/devicedata/monthly'),
          params: {
            month: this.value1,
            // city: '上海市'
          }
        }).then((res) => {
          if (res.data) {
            // this.chartData1 = res.data || {}
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
        for(let [key, value] of Object.entries(dustData)) {
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
          },{
            name: '降尘自动监测仪平均值',
            type: 'line',
            data: chartSeries.map(i=>avgDate)
          }]
        }
        this.chartBar1 = echarts.init(document.getElementById('J_chartBarBox1'))
        this.chartBar1.setOption(option)
        window.addEventListener('resize', () => {
          this.chartBar1.resize()
        })
      },
      changeChartType(type) {
        if (type == this.chartType) return

        this.chartType = type
        this.initChart2(this.chartData2)
      }
    }
  }
</script>

<style lang="scss" scoped src="./tongji.scss"></style>


