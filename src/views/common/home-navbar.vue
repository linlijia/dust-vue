<template>
  <div class="home-nav">
      <p class="h">降尘自动监测系统管理平台</p>
      <div class="desc">
          <p>上海市环境监测中心</p>
        <p>上海市曌阳智能科技有限公司</p>
      </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        updatePassowrdVisible: false
      }
    },
    components: {},
    computed: {},
    methods: {
    }
  }
</script>

<style lang="scss" scoped>
.home-nav {
    position: relative;
    height: 80px;
    background-color: #269278;
    text-align: center;
    color: #fff;
    // margin-left: 230px;

    .h {
        font-size: 24px;
        height: 80px;
    line-height: 80px;
    }
    .desc {
        position: absolute;
        right: 20px;
        bottom: 2px;
        font-size: 12px;
        text-align: right;
    }
}
</style>
