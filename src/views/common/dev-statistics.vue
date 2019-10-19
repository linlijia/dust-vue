<template>
  <el-card :body-style="{padding: '0px'}" shadow="always">
    <el-row class="devContainer">
      <el-col :span="12">
        <div class="deviceNumber">
          总设备数
          <div class="total">
            {{deviceNum.total}}
          </div>
        </div>
      </el-col>
      <el-col :span="12">
        <el-row class="offline">
          <el-col :span="12">离线</el-col>
          <el-col :span="12"><span class="number">{{deviceNum.offline}}</span></el-col>
        </el-row>
        <el-row  class="trouble" >
          <el-col :span="12">故障</el-col>
          <el-col :span="12"><span class="number">{{deviceNum.failure}}</span></el-col>
        </el-row>
      </el-col>
    </el-row>
  </el-card>
</template>

<script>
  export default {
    data () {
      return {
        deviceNum: {
          failure: 0,
          offline: 0,
          online: 0,
          total: 0
        },
        num: 0
      }
    },
    mounted () {
      this.getData()
    },
    methods: {
      getData () {
        this.$http({
          url: this.$http.adornUrl('/generator/device/status')
        }).then(({data}) => {
          if (data.count) {
            console.log(data.count)
            console.log(this.deviceNum)
            this.deviceNum = data.count
          }
        })
      }
    }
  }
</script>

<style scoped>
  .devContainer {
    border-radius: 5px;
  }

  .deviceNumber {
    padding: 12px;
    background-color: #0ACD80;
    color: white;
  }

  .total {
    font-size: 45px;
    text-align: center;
  }

  .offline {
    padding: 12px;
    margin-left: 6px;
    background-color: #a8a5aa;
    color: white;
  }
  .trouble {
    padding: 12px;
    margin-top: 4px;
    margin-left: 6px;
    background-color: #ff1e00;
    color: white;
  }

  .number {
    width: 100%;
    font-size: 20px;
  }

</style>
