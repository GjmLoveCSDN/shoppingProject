<template>
<div>
  <!--    面包屑导航区域  -->
  <el-breadcrumb separator-class="el-icon-arrow-right">
    <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
    <el-breadcrumb-item>用户管理</el-breadcrumb-item>
    <el-breadcrumb-item>用户列表</el-breadcrumb-item>
  </el-breadcrumb>
  <el-card>
    <el-row :gutter="30">
      <el-col :span="10">
        <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
          <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
        </el-input>
      </el-col>
      <el-col :span="6">
        <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
      </el-col>
    </el-row>
<!--    用户列表table区域-->
    <el-table :data="userList" stripe>
      <el-table-column type="index" label="序号" width="70px"></el-table-column>
      <el-table-column prop="username" label="姓名"></el-table-column>
      <el-table-column prop="mobile" label="电话"></el-table-column>
      <el-table-column prop="email" label="邮箱"></el-table-column>
      <el-table-column prop="role_name" label="角色用户"></el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-switch v-model="scope.row.mg_state" @change="userStateChanged(scope.row)">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="220px">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" @click="showUpdateDialog(scope.row.id)"></el-button>
          <el-button type="danger" icon="el-icon-delete" @click="deleteUser(scope.row.id)"></el-button>
          <el-tooltip class="item" effect="dark" content="分配角色" placement="top">
            <el-button type="warning" icon="el-icon-setting"></el-button>
          </el-tooltip>
        </template>
      </el-table-column>
    </el-table>
<!--    分页区域-->
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[1, 2, 3, 4]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
<!--    添加用户对话框组件-->
    <el-dialog
      title="添加用户"
      :visible.sync="addDialogVisible"
      width="50%"
    @close="addDialogClosed">
      <el-form ref="usersForm" :rules="addUserRule" :model="addUserform" label-width="80px">
        <el-form-item label="姓名" prop="username">
          <el-input v-model="addUserform.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input type="password" v-model="addUserform.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addUserform.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input v-model="addUserform.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="addDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="addUser">确 定</el-button>
  </span>
    </el-dialog>
<!--    修改用户对话框 -->
    <el-dialog
      title="修改用户"
      :visible.sync="updateDialogVisible"
      width="50%"
      @close="updateDialogClosed">
      <el-form ref="updateUsersRef" :rules="updateUserRule" :model="updateUserForm" label-width="80px">
        <el-form-item label="姓名" prop="username">
          <el-input v-model="updateUserForm.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="updateUserForm.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input v-model="updateUserForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="updateDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="updateUser">确 定</el-button>
  </span>
    </el-dialog>
  </el-card>
</div>
</template>

<script>
export default {
  name: 'Users',
  data () {
    var checkEmail = (rule, value, callBack) => {
      const regEmail = /^\w+@[a-z0-9]+\.[a-z]{2,4}$/
      if (regEmail.test(value)) {
        return callBack()
      }
      callBack(new Error('请输入合法的邮箱！'))
    }
    var checkMobile = (rule, value, callBack) => {
      const regMobile = /^(((13[0-9])|(14[5-7])|(15[0-9])|(17[0-9])|(18[0-9]))+\d{8})$/
      if (regMobile.test(value)) {
        return callBack()
      }
      callBack(new Error('请输入合法的手机号码！'))
    }
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userList: [],
      total: 0,
      addDialogVisible: false,
      updateDialogVisible: false,
      addUserform: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      addUserRule: {
        username: [
          { required: true, message: '请输入姓名', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在3-5个字符之间', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 12, message: '长度在6-15个字符之间', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入电话', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ]
      },
      updateUserForm: {},
      updateUserRule: {
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入电话', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ]
      }
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList () {
      const { data: ref } = await this.$http.get('users', { params: this.queryInfo })
      if (ref.meta.status !== 200) return this.$message.error('获取数据失败！')
      console.log(ref.data)
      this.total = ref.data.total
      this.userList = ref.data.users
    },
    handleSizeChange (newPageSize) {
      this.queryInfo.pagesize = newPageSize
      this.getUserList()
    },
    handleCurrentChange (newPageNum) {
      this.queryInfo.pagenum = newPageNum
      this.getUserList()
    },
    async userStateChanged (userinfo) {
      console.log(userinfo.mg_state)
      const { data: ref } = await this.$http.put(`users/${userinfo.id}/state/${userinfo.mg_state}`)
      if (ref.meta.status !== 200) {
        userinfo.mg_state = !userinfo.mg_state
        return this.$message.error('更新失败！')
      }
      this.$message.success('更新成功！')
    },
    addDialogClosed () {
      // 表单重置
      this.$refs.usersForm.resetFields()
    },
    // 提交表单
    addUser () {
      this.$refs.usersForm.validate(async valid => {
        if (!valid) return this.$message.error('输入的数据不合法，请重新输入')
        const { data: ref } = await this.$http.post('/users', this.addUserform)
        if (ref.meta.status !== 201) return this.$message.error('添加用户失败！')
        this.$message.success('添加成功')
        this.addDialogVisible = false
        this.getUserList()
      })
    },
    async showUpdateDialog (id) {
      const { data: res } = await this.$http.get('users/' + id)
      if (res.meta.status !== 200) return this.$message.error('未找到该用户信息')
      this.updateUserForm = res.data
      this.updateDialogVisible = true
    },
    async deleteUser (id) {
      const confirmResult = await this.$confirm('删除用户', '删除用户', {
        confirmButtonText: '确认',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      if (confirmResult !== 'confirm') return this.$message.info('取消删除')
      const { data: res } = await this.$http.delete('users/' + id)
      if (res.meta.status !== 200) return this.$message.error('删除失败')
      this.getUserList()
      this.$message.success('删除成功')
    },
    updateDialogClosed () {
      this.$refs.updateUsersRef.resetFields()
    },
    // 更新用户
    updateUser () {
      this.$refs.updateUsersRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.put('users/' + this.updateUserForm.id,
          {
            email: this.updateUserForm.email,
            mobile: this.updateUserForm.mobile
          })
        if (res.meta.status !== 200) return this.$message.error('修改失败')
        this.updateDialogVisible = false
        this.getUserList()
        this.$message.success('添加成功')
      })
    }
  }
}
</script>

<style scoped>

</style>
