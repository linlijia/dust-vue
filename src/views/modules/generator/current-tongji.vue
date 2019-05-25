<template>
  <div class="mod-tongji">
     <el-row :gutter="20">
 
      <el-col :span="24" class="mod-col">
        <!-- <el-card> -->
          <div class="flex-icon-bar">
            <h2>监测点实时数据统计图</h2>
            
            <div>
              <i class="iconfont icon-linechart"></i>
              <i class="iconfont icon-barchart"></i>
            </div>
          </div>
          <div class="tongji-filer">
              <!-- <el-date-picker
                v-model="value2"
                type="month"
                :default-value="new Date()"
                value-format="yyyy-MM"
                placeholder="选择月"
                @change="getChartData2">
              </el-date-picker> -->
              <el-date-picker
                v-model="value2"
                type="datetimerange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                value-format="yyyy-MM-dd HH:mm:ss">
              </el-date-picker>
              <el-select v-model="curSite" placeholder="站点">
                <el-option v-for="(item,index) in stieLists" :key="index" :label="item.site" :value="item.id"></el-option>
              </el-select>
              <el-button @click="getChartData2" >查询</el-button>
            </div>
          <div id="J_chartBarBox2" class="chart-box"></div>
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
        value2: '',
        // value2: ['2019-01-03 00:00:00', '2019-02-26 00:00:00'],
        chartData2: {},
        chartType: 'line',
        curSite: '',
        stieLists: []
      }
    },
    mounted () {
      this.getSiteList()
    },
    methods: {
      getChartData2() {
        this.$http({
          url: this.$http.adornUrl('/generator/site/details/'+this.curSite),
          params: {
            start: this.value2[0],
            end: this.value2[1]
          }
        }).then((res) => {
          if (res.data) {
            this.chartData2 = res.data.data.dustData || []
            this.initChart2(res.data.data.dustData || [])
          }
        })
      },
      initChart2(data = {}) {
        var chartType = this.chartType
        var dustData = data || {}
        var chartLegend = []
        var chartxAxis = []
        var chartSeries = []
        for (let [key, value] of Object.entries(dustData)) {
          chartLegend.push(key)
          let _s = []
          if (value && value.length) {
            value.forEach(i => {
              chartxAxis.push(i.dataTime)
              _s.push(i.a34011Rtd)
            })
          }
          chartSeries.push({
            name: key,
            data: _s,
            type: chartType
          })
        }
        var option = {
          title: {
            text: '',
            subtext: ''
          },
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow'
            },
            formatter: '{b}<br/> {a} : {c} g'
          },
          legend: {
            data: chartLegend
          },
          grid: {
            left: '8%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: {
            data: chartxAxis
          },
          yAxis: {
            type: 'value',
            name: '月度称量增重（单位：g）'
          },
          series: chartSeries,
        };
        this.chartBar2 = echarts.init(document.getElementById('J_chartBarBox2'))
        this.chartBar2.setOption(option)
        window.addEventListener('resize', () => {
          this.chartBar2.resize()
        })
      },
      changeChartType(type) {
        if (type == this.chartType) return

        this.chartType = type
        this.initChart2(this.chartData2)
      },
      getSiteList() {
        this.$http({
          url: this.$http.adornUrl('/generator/site/list?limit=1008611')
        }).then(({data}) => {
          if (data.page) {
            this.stieLists = data.page.list || []
            this.curSite = data.page.list[0] && data.page.list[0].id
            this.getChartData2()
          }
        })
      }
    }
  }
</script>

<style lang="scss" scoped src="./tongji.scss"></style>


