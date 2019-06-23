<template>
  <div class="mod-home">
    <el-row :gutter="10">
      <el-col :span="24" class="city">
        <el-select size="small" v-model="cityList[0]">
          <el-option v-for="item in cityList" :value="item" :key="item" :label="item"></el-option>
        </el-select>
      </el-col>
      <el-col :span="12">
        <el-row class="weather">
          <el-col :span="8">
            <p class="w-city">{{weatherData.city}}</p>
            <p>{{weatherData.time || nowDate}}</p>
          </el-col>
          <el-col :span="8">
            <p>{{weatherData.wendu}}°C</p>
            <p>{{weatherData.type}}</p>
          </el-col>
          <el-col :span="8">
            <p>总设备数：{{num.total || 0}}</p>
            <p>在线设备数：{{num.online || 0}}</p>
            <p>故障设备数：{{num.failure || 0}}</p>
          </el-col>
        </el-row>
        <el-table
          :data="dataList"
          border
          v-loading="dataListLoading"
          class="home-table"
          style="width: 100%;"
          max-height="710">
          <el-table-column
            type="index"
            label=" "
            header-align="center"
            align="center">
          </el-table-column>
          <el-table-column
            prop="siteName"
            header-align="center"
            align="center"
            label="监测站点">
          </el-table-column>
          <el-table-column
            prop="weight"
            header-align="center"
            align="center"
            :formatter="(row, column, cellValue)=>{return (cellValue-0).toFixed(4)}"
            label="月称重量(g)">
          </el-table-column>
          <el-table-column
            prop="dust"
            header-align="center"
            align="center"
            :formatter="(row, column, cellValue)=>{return (cellValue-0).toFixed(2)}"
            label="月降尘量(t/km²*M)">
            <template slot-scope="scope">
              <span style="color:red">{{ scope.row.dust }}</span>
            </template>
          </el-table-column>
          <el-table-column
            prop="effectiveDay"
            header-align="center"
            align="center"
            label="有效集尘天数">
            <template slot-scope="scope">
              <span style="color:red">{{ scope.row.effectiveDay }}</span>
            </template>
          </el-table-column>
          <el-table-column
            prop="weighingTime"
            header-align="center"
            align="center"
            label="称量日期">
          </el-table-column>
        </el-table>
      </el-col>
      <el-col :span="12">
        <HomeMap :num="num"></HomeMap>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import HomeMap from '@/components/home-map'
  export default {
    data () {
      return {
        num: {
          failure: 0,
          offline: 0,
          online: 0,
          total: 0},
        dataList: [],
        dataListLoading: false,

        cityList: ['上海市'],
        nowDate: new Date().getFullYear() + '-' + (new Date().getMonth() + 1) + '-' + new Date().getDate(),

        weatherData: {}
      }
    },
    components: {
      HomeMap
    },
    methods: {
      getNumData () {
        this.$http({
          url : this.$http.adornUrl('/generator/device/status')
        }).then(({data}) => {
          if(data.count) {
            this.num = data.count
          }
        })
      },
      getWeather () {
        this.$http({
          url: this.$http.adornUrl('/generator/weather/101020100')
        }).then(({data}) => {
          if(data.data) {
            this.weatherData = {
              city : data.data.cityInfo && data.data.cityInfo.city || '上海市',
              time : data.data.data.forecast && data.data.data.forecast[0] && data.data.data.forecast[0].ymd,
              type : data.data.data.forecast && data.data.data.forecast[0] && data.data.data.forecast[0].type,
              wendu : data.data.data.wendu
            }
          }
       })
      }
    },
    mounted () {
      this.getNumData()
      this.getWeather()
      this.$http({
        url : this.$http.adornUrl('/generator/site/map/list')
      }).then(({data}) => {
          if(data.data) {
            this.dataList = data.data ||  []
          }
      })
    },
  }
</script>

<style lang="scss">
  .mod-home {
    .city {
      margin: 0 0 5px 0;
    }
    .weather {
      background-color: #59d0aa;
      padding: 10px 20px;
      color: #fff;
      line-height: 40px;
    }
    .w-city {
      font-size: 20px;
    }
  }
  .home-table {


  }
</style>

