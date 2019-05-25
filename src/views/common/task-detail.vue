<template>
  <div class="mod-tdetail">

    <el-row :gutter="20">
      <el-col :span="24" class="mod-col">
        <el-card>
            <h2>基本信息</h2>
            <el-row>
                <el-col :span="8">ddd:cccc</el-col>
                <el-col :span="8">zxzd:sdds</el-col>

                <el-col :span="24">
                    <div class="pic-list clearfix">
                        <div class="pic-item" v-for="item in [1,2,3,4,7,5,65]" :key="item">
                            <img src="https://tva3.sinaimg.cn/crop.0.0.750.750.180/005BOKE5jw8f26vv4x3vpj30ku0kvwfq.jpg"/>
                        </div>
                    </div>
                </el-col>
            </el-row>
        </el-card>
      </el-col>
      <el-col :span="24" class="mod-col">
        <el-card>
            <h2>记录表详情</h2>
        </el-card>
      </el-col>
      <el-col :span="24" class="mod-col">
        <el-card>
            <h2>操作日志</h2>
            <el-table :data='list'>
                <el-table-column prop="" label="序号"></el-table-column>
                <el-table-column prop="x" label="操作人"></el-table-column>
                <el-table-column prop="op" label="操作时间"></el-table-column>
                <el-table-column prop="transport" label="操作内容"></el-table-column>
            </el-table>
        </el-card>
      </el-col>
    </el-row>

  </div>
</template>

<script>

  export default {
    data () {
      return {
          list: [

          ]
      }
    },
    mounted () {
      console.log(this.$route.params.id)
    },
    methods: {

    }
  }
</script>

<style lang="scss" scoped>
  .mod-tdetail {
      .mod-col {
          margin-bottom: 15px;
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
  }
</style>

