<template>
<div>
  <el-button type="info" @click="loginout">注销</el-button>
</div>
</template>

<script>
export default {
  name: 'Home',
  methods: {
    // 注销登录
    loginout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>

<style scoped>

</style>
