<template>
  <div class="mod-tdetail">

    <el-row :gutter="20">
      <el-col :span="24" class="mod-col">
        <el-card>
            <h2>设备基本信息</h2>
            <el-row :gutter="10">
                <el-col :span="8">序号：{{baseInfo.id}}</el-col>
                <el-col :span="8">站点ID：{{baseInfo.siteId}}</el-col>
                <el-col :span="8">MN码：{{baseInfo.mn}}</el-col>
                <el-col :span="8">接口根路径：{{baseInfo.interfacePath}}</el-col>
                <el-col :span="8">服务端信息：{{baseInfo.serviceInfo}}</el-col>
                <el-col :span="8">服务端密码：{{baseInfo.passwd}}</el-col>
                <el-col :span="8">系统类型：{{baseInfo.systemType}}</el-col>
                <el-col :span="8">串口名：{{baseInfo.serialPortType}}</el-col>
                <el-col :span="8">温度湿度限制：{{baseInfo.temperatureHumidityLimit}}</el-col>
                <el-col :span="8">称量间隔（小时）：{{baseInfo.uploadInterval}}</el-col>
                <el-col :span="8">添加时间：{{baseInfo.createAt}}</el-col>
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
      <el-col :span="24" class="mod-col">
        <el-card>
            <h2>设备图片</h2>
            <el-table
            :data="devicePicList"
            border
            v-loading="devicePicListLoading"
            style="width: 100%;">
                <el-table-column
                    prop="id"
                    header-align="center"
                    align="center"
                    label="序号">
                </el-table-column>
                <el-table-column
                    prop="mn"
                    header-align="center"
                    align="center"
                    label="设备mn">
                </el-table-column>
                <el-table-column
                    prop="path"
                    header-align="center"
                    align="center"
                    label="图片路径">
                </el-table-column>
                <el-table-column
                    prop="dateTime"
                    header-align="center"
                    align="center"
                    label="上传时间">
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
        </el-card>
      </el-col>

      <el-col :span="24" class="mod-col">
        <el-card>
            <h2>最近24小时数据</h2>
            <div id="J_chartBarBox" class="chart-box"></div>
        </el-card>
      </el-col>
      <!-- <el-col :span="24" class="mod-col">
        <el-card>
            <h2>设备信息</h2>
            <el-table
            :data="deviceList"
            border
            v-loading="dataListLoading"
            @row-click="handleRowClick"
            style="width: 100%;">
            <el-table-column
                prop="id"
                header-align="center"
                align="center"
                label="序号">
            </el-table-column>
            <el-table-column
                prop="siteId"
                header-align="center"
                align="center"
                label="站点ID">
            </el-table-column>
            <el-table-column
                prop="mn"
                header-align="center"
                align="center"
                label="MN码">
            </el-table-column>
            <el-table-column
                prop="interfacePath"
                header-align="center"
                align="center"
                label="接口根路径">
            </el-table-column>
            <el-table-column
                prop="serviceInfo"
                header-align="center"
                align="center"
                label="服务端信息">
            </el-table-column>
            <el-table-column
                prop="passwd"
                header-align="center"
                align="center"
                label="服务端密码">
            </el-table-column>
            <el-table-column
                prop="systemType"
                header-align="center"
                align="center"
                label="系统类型">
            </el-table-column>
            <el-table-column
                prop="serialPortType"
                header-align="center"
                align="center"
                label="串口名">
            </el-table-column>
            <el-table-column
                prop="temperatureHumidityLimit"
                header-align="center"
                align="center"
                label="温度湿度限制">
            </el-table-column>
            <el-table-column
                prop="uploadInterval"
                header-align="center"
                align="center"
                label="称量间隔（小时）">
            </el-table-column>
            <el-table-column
                prop="createAt"
                header-align="center"
                align="center"
                label="添加时间">
            </el-table-column>
            </el-table>
        </el-card>
      </el-col> -->
    </el-row>

  </div>
</template>

<script>
    import echarts from 'echarts'
  export default {
    data () {
      return {
          baseInfo: {},
          devicePicList: [],
          devicePicListLoading: true,
          pageIndex: 1,
            pageSize: 10,
            totalPage: 0,

            chartBar: null,
            chartData: []
      }
    },
    mounted () {
      if(!this.$route.params.id) {
          //
          return
      };
      this.$http({
            url : this.$http.adornUrl('/generator/device/info/' + this.$route.params.id)
        }).then(({data}) => {
            if(data.device) {
                this.baseInfo = data.device

                this.getDevicePic()
                this.getChartData()
            }
        })
    },
    activated () {
      // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
      if (this.chartBar) {
        this.chartBar.resize()
      }
    },
    methods: {
        getChartData () {
            if(!this.baseInfo.mn) return
            this.$http({
                url : this.$http.adornUrl('/generator/devicedata/drawList'),
                params: {
                    'mn' : this.baseInfo.mn
                }
            }).then(({data}) => {
                if(data.data) {
                    this.chartData = data.data || []

                    this.initChartBar()
                }
            })
        },
        getDevicePic() {
            this.devicePicListLoading = true
            this.$http({
                url : this.$http.adornUrl('/generator/deviceimage/list'),
                params: {
                    'mn' : this.baseInfo.mn,
                    'page': this.pageIndex
                }
            }).then(({data}) => {
                if(data.page) {
                    this.devicePicList = data.page.list || []
                }
                this.devicePicListLoading = false
            })
        },
        // 每页数
        sizeChangeHandle (val) {
            this.pageSize = val
            this.pageIndex = 1
            this.getDevicePic()
        },
        // 当前页
        currentChangeHandle (val) {
            this.pageIndex = val
            this.getDevicePic()
        },

        // 图
      initChartBar () {
        var option = {
            title: {
                text: '',
                subtext: ''
            },
            tooltip: {
                trigger: 'axis',
                axisPointer: {
                    type: 'shadow'
                }
            },
            legend: {
                data: ['降尘实时数据', '降尘原始数据']
            },
            grid: {
                left: '3%',
                right: '4%',
                bottom: '3%',
                containLabel: true
            },
            xAxis: {
                type: 'category',
                boundaryGap: [0, 0.01],
                data: this.chartData.map(i => i.dataTime)
            },
            yAxis: {
                type: 'value'
            },
            series: [
                {
                    name: '降尘实时数据',
                    type: 'line',
                    data: this.chartData.map(i => i.a34011Rtd)
                },
                {
                    name: '降尘原始数据',
                    type: 'line',
                    data: this.chartData.map(i => i.a34011Ori)
                }
            ]
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
  }
</style>

