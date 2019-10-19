<template>
  <div class="mod-tdetail">

    <el-row :gutter="20">
      <el-col :span="24" class="mod-col">
        <el-card>
          <h2>基本信息</h2>
          <el-row :gutter="10">
            <!-- <el-col :span="8">序号：{{baseInfo.id}}</el-col> -->
            <el-col :span="8">站点：{{baseInfo.site}}</el-col>
            <el-col :span="8">区域：{{baseInfo.city}} {{baseInfo.district}}</el-col>
            <!-- <el-col :span="8">国家站点：{{baseInfo.superiorSite}}</el-col> -->
            <el-col :span="8">地址：{{baseInfo.address}}</el-col>
            <el-col :span="8">经纬度：{{baseInfo.latidute}},{{baseInfo.longidute}}</el-col>
            <!-- <el-col :span="8">纬度：{{baseInfo.longidute}}</el-col> -->
            <el-col :span="8">联系人：{{baseInfo.contact}}</el-col>
            <el-col :span="8">联系电话：{{baseInfo.phoneNo}}</el-col>
            <!-- <el-col :span="8">：{{baseInfo.}}</el-col> -->

            <!-- <el-col :span="24">
                <div class="pic-list clearfix">
                    <div class="pic-item" v-for="item in [1,2,3,4,7,5,65]" :key="item">
                        <img src="https://tva3.sinaimg.cn/crop.0.0.750.750.180/005BOKE5jw8f26vv4x3vpj30ku0kvwfq.jpg"/>
                    </div>
                </div>
            </el-col> -->
          </el-row>
        </el-card>
      </el-col>
      <!-- <el-col :span="24" class="mod-col">
        <el-card>
            <h2>记录表详情</h2>
        </el-card>
      </el-col> -->
      <el-col :span="24" class="mod-col">
        <el-card>
          <h2>设备运行状态</h2>
          <el-table
            :data="deviceStatusList"
            border
            fit
            v-loading="dataListLoading"
            style="width: 100%;">
            <!-- <el-table-column
              type="selection"
              header-align="center"
              align="center"
              width="50">
            </el-table-column> -->
            <el-table-column
              fixed="left"
              prop="mn"
              header-align="center"
              align="center"
              label="设备编码">
            </el-table-column>
            <el-table-column
              fixed="left"
              prop="dataTime"
              header-align="center"
              align="center"
              label="数据时间">
            </el-table-column>
            <el-table-column
              prop="currentStatus"
              header-align="center"
              align="center"
              :formatter="formatStatus"
              label="设备状态">
            </el-table-column>
            <el-table-column
              prop="operation"
              header-align="center"
              align="center"
              :formatter="formatOperation"
              label="当前操作">
            </el-table-column>
            <el-table-column
              prop="troubleSet"
              header-align="center"
              align="center"
              :formatter="formatTrouble"
              label="故障情况">
            </el-table-column>
            <el-table-column
              prop="weight"
              header-align="center"
              align="center"
              label="重量">
            </el-table-column>
            <el-table-column
              prop="doorStatus"
              header-align="center"
              align="center"
              :formatter="formatDoorStatus"
              label="门状态">
            </el-table-column>
            <el-table-column
              prop="trayStatus"
              header-align="center"
              align="center"
              :formatter="formatTrayStatus"
              label="托盘状态">
            </el-table-column>
            <el-table-column
              prop="rainfall"
              header-align="center"
              align="center"
              label="降雨量">
            </el-table-column>
            <el-table-column
              prop="outTemperature"
              header-align="center"
              align="center"
              label="环境温度">
            </el-table-column>
            <el-table-column
              prop="outHumidity"
              header-align="center"
              align="center"
              label="环境湿度">
            </el-table-column>
            <el-table-column
              prop="entranceTemperature"
              header-align="center"
              align="center"
              label="入口温度">
            </el-table-column>
            <el-table-column
              prop="entranceHumidity"
              header-align="center"
              align="center"
              label="入口湿度">
            </el-table-column>
            <el-table-column
              prop="exitTemperature"
              header-align="center"
              align="center"
              label="出口温度">
            </el-table-column>
            <el-table-column
              prop="exitHumidity"
              header-align="center"
              align="center"
              label="出口湿度">
            </el-table-column>
            <el-table-column
              prop="plateTemperature1"
              header-align="center"
              align="center"
              label="热盘温度1">
            </el-table-column>
            <el-table-column
              prop="plateTemperature2"
              header-align="center"
              align="center"
              label="热盘温度2">
            </el-table-column>
            <el-table-column
              prop="plateTemperature3"
              header-align="center"
              align="center"
              label="热盘温度3">
            </el-table-column>
            <el-table-column
              prop="airTemperature"
              header-align="center"
              align="center"
              label="温控温度">
            </el-table-column>
            <el-table-column
              prop="waterTemperature"
              header-align="center"
              align="center"
              label="水蒸气水温">
            </el-table-column>
            <el-table-column
              prop="trayPosition11"
              header-align="center"
              align="center"
              label="电位器11">
            </el-table-column>
            <el-table-column
              prop="trayPosition12"
              header-align="center"
              align="center"
              label="电位器12">
            </el-table-column>
            <el-table-column
              prop="trayPosition21"
              header-align="center"
              align="center"
              label="电位器21">
            </el-table-column>
            <el-table-column
              prop="trayPosition22"
              header-align="center"
              align="center"
              label="电位器22">
            </el-table-column>
            <el-table-column
              prop="up1Limit"
              header-align="center"
              align="center"
              label="托盘上限位1">
            </el-table-column>
            <el-table-column
              prop="up2Limit"
              header-align="center"
              align="center"
              label="托盘上限位2">
            </el-table-column>
            <el-table-column
              prop="down1Limit"
              header-align="center"
              align="center"
              label="托盘下限位1">
            </el-table-column>
            <el-table-column
              prop="down2Limit"
              header-align="center"
              align="center"
              label="托盘下限位2">
            </el-table-column>
            <el-table-column
              prop="openLimit"
              header-align="center"
              align="center"
              label="开门限位">
            </el-table-column>
            <el-table-column
              prop="closeLimit"
              header-align="center"
              align="center"
              label="关门限位">
            </el-table-column>
            <el-table-column
              prop="bucketTrigger"
              header-align="center"
              align="center"
              label="水箱水位">
            </el-table-column>
            <el-table-column
              prop="steamTrigger"
              header-align="center"
              align="center"
              label="水蒸汽水位">
            </el-table-column>
            <el-table-column
              prop="balanceFlag"
              header-align="center"
              align="center"
              label="校准结果">
            </el-table-column>
            <el-table-column
              prop="balanceMode"
              header-align="center"
              align="center"
              label="称量状态">
            </el-table-column>
            <el-table-column
              prop="printMode"
              header-align="center"
              align="center"
              label="数据有效性">
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>

      <el-col :span="24" class="mod-col">
        <el-card>
          <div class="flex-icon-bar">
            <h2>实时数据</h2>
            <div>
              <i class="iconfont icon-linechart" @click="changeChartType('line')"></i>
              <i class="iconfont icon-barchart" @click="changeChartType('bar')"></i>
            </div>
          </div>
          <div id="J_chartBarBox" class="chart-box"></div>
        </el-card>
      </el-col>
    </el-row>

  </div>
</template>

<script>
  import echarts from 'echarts'

  export default {
    data() {
      return {
        baseInfo: {},
        deviceStatusList: [],
        dataListLoading: true,
        chartBar: null,
        chartData: [],
        chartType: 'line'
      }
    },
    watch: {
      // 如果路由有变化，会再次执行该方法
      "$route": "pageInit"
    },
    mounted() {
      this.pageInit()
    },
    methods: {
      pageInit() {
        if (!this.$route.params.id) {
          //
          return
        }
        ;

        var that = this

        this.$http({
          url: this.$http.adornUrl('/generator/site/info/' + this.$route.params.id)
        }).then(({data}) => {

          if (data.site) {
            this.baseInfo = data.site
            this.getChartData()

          }
        })

        this.$http({
          url: this.$http.adornUrl('/generator/devicestatus/list?limit=1008611'),
          params: {siteId: this.$route.params.id}
        }).then(({data}) => {
          if (data.page) {
            this.deviceStatusList = data.page.list || []
          }
          this.dataListLoading = false
        })
      },
      detailClick(id) {
        this.$router.push({
          name: 'devdetail',
          params: {id: id}
        })
      },

      getChartData() {
        if (!this.baseInfo.id) return

        this.$http({
          url: this.$http.adornUrl('/generator/site/details/' + this.baseInfo.id)
        }).then((res) => {
          if (res.data) {

            this.chartData = res.data.data.dustData || []

            this.initChartBar()
          }
        })
      },

      changeChartType (type) {
        if (type === this.chartType) return

        this.chartType = type
        this.initChartBar()
      },

      initChartBar() {
        var chartType = this.chartType
        var dustData = this.chartData || {}
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
          xAxis: {
            data: chartxAxis
          },
          yAxis: {
            type: 'value',
            name: '月度称量增重（单位：g）'
          },
          series: chartSeries,
          grid: {left: '8%', right: '4%', bottom: '3%', containLabel: true}
        };
        this.chartBar = echarts.init(document.getElementById('J_chartBarBox'))
        this.chartBar.setOption(option)
        window.addEventListener('resize', () => {
          this.chartBar.resize()
        })
      },
    }
  }
</script>

<style lang="scss" scoped>
  .mod-tdetail {
    .mod-col {
      margin-bottom: 15px;

      .el-col {
        margin: 5px 0;
      }
    }

    .pic-list {
      .pic-item {
        display: inline-block;
        width: 160px;
        height: 160px;
        margin: 0 10px 10px 0;
        img {
          width: 100%;
          height: 100%;
        }
      }
    }

    .chart-box {
      min-height: 500px;
    }
    .flex-icon-bar {
      display: flex;
      justify-content: space-between;

    }
    .iconfont {
      font-size: 21px;
    }
  }

</style>

