<template>
  <el-container class="home_container">
    <el-header >
      <div>
        <img src="../assets/blackImg.png" alt="">
        <span>电商管理平台系统</span>
      </div>
      <el-button type="info" @click="loginout">注销</el-button>
    </el-header>
    <el-container>
      <el-aside :width="isCollapse ? '64px':'200px'">
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <el-menu background-color="#404040" router text-color="white" active-text-color="#409EFF" unique-opened :collapse="isCollapse"
        :collapse-transition="false" :default-active="activePath">
          <el-submenu :index="item.id+''" v-for="item in menuList" :key="item.id">
            <template slot="title">
              <i :class="iconObj[item.id]"></i>
              <span >{{ item.authName }}</span>
            </template>
            <el-menu-item :index="'/'+subItem.path" v-for="subItem in item.children" :key="subItem.id" @click="saveNavState('/'+subItem.path)">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span >{{ subItem.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  created () {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  data () {
    return {
      menuList: [],
      iconObj: {
        125: 'iconfont icon-user',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      // 是否折叠
      isCollapse: false,
      activePath: ''
    }
  },
  methods: {
    // 注销登录
    loginout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList () {
      const { data: ref } = await this.$http.get('menus')
      if (ref.meta.status !== 200) return this.$message.error(ref.meta.msg)
      this.menuList = ref.data
    },
    toggleCollapse () {
      this.isCollapse = !this.isCollapse
    },
    saveNavState (activePath) {
      window.sessionStorage.setItem('activePath', activePath)
    }
  }
}
</script>

<style lang="less" scoped>
.home_container {
  height: 100%;
}
.el-header {
  background-color: #333333;
  display: flex;
  justify-content: space-between;
  padding-left: 0px;
  align-items: center;
  color: #cccccc;
  font-size: 35px;
  > div {
    display: flex;
    align-items: center;
    span {
      margin-left: 20px;
    }
  }
}
.el-aside {
  background-color: #404040;
  .el-menu {
    border-right: none;
  }
}
.el-main {
  background-color: #cccccc;
}
.iconfot {
  margin-right: 15px;
}
.toggle-button {
  background-color: #404040;
  font-size: 12px;
  line-height: 24px;
  color: #cccccc;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>
