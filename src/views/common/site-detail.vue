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
              prop="s10001"
              header-align="center"
              align="center"
              label="X轴偏移量">
            </el-table-column>
            <el-table-column
              prop="s10002"
              header-align="center"
              align="center"
              label="Y轴偏移量">
            </el-table-column>
            <el-table-column
              prop="s10003"
              header-align="center"
              align="center"
              label="热盘温度">
            </el-table-column>
            <el-table-column
              prop="s10004"
              header-align="center"
              align="center"
              label="降尘室温度">
            </el-table-column>
            <el-table-column
              prop="s10005"
              header-align="center"
              align="center"
              label="降尘室温度">
            </el-table-column>
            <el-table-column
              prop="s10006"
              header-align="center"
              align="center"
              label="测量室温度">
            </el-table-column>
            <el-table-column
              prop="s10007"
              header-align="center"
              align="center"
              label="测量室湿度">
            </el-table-column>
            <el-table-column
              prop="s10008"
              header-align="center"
              align="center"
              label="电源电压">
            </el-table-column>
            <el-table-column
              prop="s10009"
              header-align="center"
              align="center"
              label="电池电压">
            </el-table-column>
            <el-table-column
              prop="s10010"
              header-align="center"
              align="center"
              label="经度">
            </el-table-column>
            <el-table-column
              prop="s10011"
              header-align="center"
              align="center"
              label="纬度">
            </el-table-column>
            <el-table-column
              prop="s10012"
              header-align="center"
              align="center"
              label="高度">
            </el-table-column>
            <el-table-column
              prop="s10013"
              header-align="center"
              align="center"
              label="门状态">
            </el-table-column>
            <el-table-column
              prop="s10014"
              header-align="center"
              align="center"
              label="托盘状态">
            </el-table-column>
            <el-table-column
              prop="s10015"
              header-align="center"
              align="center"
              label="降水状态">
            </el-table-column>
            <el-table-column
              prop="s10016"
              header-align="center"
              align="center"
              label="水箱水位状态">
            </el-table-column>
            <el-table-column
              prop="s10017"
              header-align="center"
              align="center"
              label="机芯上电状态">
            </el-table-column>
            <el-table-column
              prop="s10018"
              header-align="center"
              align="center"
              label="机芯校准状态">
            </el-table-column>
            <el-table-column
              prop="operation"
              header-align="center"
              align="center"
              label="操作">
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

      changeChartType(type) {
        if (type == this.chartType) return

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

