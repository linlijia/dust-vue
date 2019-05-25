<template>
  <nav class="site-navbar" :class="'site-navbar--' + navbarLayoutType">
    <!-- <template v-if="!isHome">
      
    <div class="site-navbar__header">
      <h1 class="site-navbar__brand" @click="$router.push({ name: 'home' })">
        <a class="site-navbar__brand-lg" href="javascript:;">降尘自动监测系统</a>
        <a class="site-navbar__brand-mini" href="javascript:;">降尘</a>
      </h1>
    </div>
    
      <div class="site-navbar__body clearfix">
          <el-menu
            class="site-navbar__menu"
            mode="horizontal">
            <el-menu-item class="site-navbar__switch" index="0" @click="sidebarFold = !sidebarFold">
              <icon-svg name="zhedie"></icon-svg>
            </el-menu-item>
          </el-menu>
          <el-menu
            class="site-navbar__menu site-navbar__menu--right"
            mode="horizontal">
            <el-menu-item class="site-navbar__avatar" index="3">
              <el-dropdown :show-timeout="0" placement="bottom">
                <span class="el-dropdown-link">
                  <img src="~@/assets/img/avatar.png" :alt="userName">{{ userName }}
                </span>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item @click.native="updatePasswordHandle()">个人设置</el-dropdown-item>
                  <el-dropdown-item @click.native="logoutHandle()">退出</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </el-menu-item>
          </el-menu>
        </div>
      </template>
      <template v-else> -->
        <div class="home-nav">
            <!-- <el-menu
              class="site-navbar__menu site-navbar__menu--left"
              mode="horizontal">
              <el-menu-item class="site-navbar__avatar" index="3"> -->
                <div class="menu-user">
                  <el-dropdown :show-timeout="0" placement="bottom">
                    <span class="menu-user-name">欢迎您，{{ userName }}</span>
                    <el-dropdown-menu slot="dropdown">
                      <el-dropdown-item @click.native="updatePasswordHandle()">修改密码</el-dropdown-item>
                      <el-dropdown-item @click.native="logoutHandle()">退出</el-dropdown-item>
                    </el-dropdown-menu>
                  </el-dropdown>
                </div>
              <!-- </el-menu-item>
            </el-menu> -->
            <p class="h">降尘自动监测系统管理平台</p>
            <div class="desc">
                <p>上海市环境监测中心</p>
              <p>上海市曌阳智能科技有限公司</p>
            </div>
        </div>
      <!-- </template> -->
    <!-- 弹窗, 修改密码 -->
    <update-password v-if="updatePassowrdVisible" ref="updatePassowrd"></update-password>
  </nav>
</template>

<script>
  import UpdatePassword from './main-navbar-update-password'
  import { clearLoginInfo } from '@/utils'
  export default {
    props: {
      isHome : Boolean
    },
    data () {
      return {
        updatePassowrdVisible: false,
        // isHome: false
      }
    },
    // watch: {
    //   '$route':'getPath'
    // },
    components: {
      UpdatePassword
    },
    computed: {
      navbarLayoutType: {
        get () { return this.$store.state.common.navbarLayoutType }
      },
      sidebarFold: {
        get () { return this.$store.state.common.sidebarFold },
        set (val) { this.$store.commit('common/updateSidebarFold', val) }
      },
      mainTabs: {
        get () { return this.$store.state.common.mainTabs },
        set (val) { this.$store.commit('common/updateMainTabs', val) }
      },
      userName: {
        get () { return this.$store.state.user.name }
      }
    },
    created () {
      // this.getPath(this.$route)
    },
    methods: {
      // getPath (route) {
      //   if(route.name == 'home') {
      //     this.isHome = true
      //   }
      //   else {
      //     this.isHome = false
      //   }
      // },
      // 修改密码
      updatePasswordHandle () {
        this.updatePassowrdVisible = true
        this.$nextTick(() => {
          this.$refs.updatePassowrd.init()
        })
      },
      // 退出
      logoutHandle () {
        this.$confirm(`确定进行[退出]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/sys/logout'),
            method: 'post',
            data: this.$http.adornData()
          }).then(({data}) => {
            if (data && data.code === 0) {
              clearLoginInfo()
              this.$router.push({ name: 'login' })
            }
          })
        }).catch(() => {})
      }
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
.menu-user {
  position: absolute;
        left: 20px;
        bottom: 5px;
}
.menu-user-name {
  color: #fff;
}
</style>
