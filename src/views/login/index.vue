<template>
  <div class="login-container">
    <el-form class="login-form" :model="loginForm" ref="loginForm" :rules="rules"  label-position="left"
             label-width="0px" autoComplete="on">
      <h3 class="login_title">系统登录</h3>
      <div class="login-body">
        <el-form-item prop="username">
          <el-input type="text" v-model="loginForm.username" auto-complete="off" placeholder="账号"></el-input>
          <div class="login-username" slot="prepend"></div>
        </el-form-item>
        <el-form-item prop="password">
          <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="密码"></el-input>
          <template slot="prepend"><div class="login-password"></div></template>
        </el-form-item>
        <!--<el-checkbox class="login_remember" v-model="checked" label-position="left">记住密码</el-checkbox>-->
        <el-form-item style="width: 100%">
          <el-button type="primary" @click.native.prevent="doLogin" style="width: 100%">登录</el-button>
        </el-form-item>
      </div>
    </el-form>
  </div>
</template>

<script>
import { getToken } from '@/api/auth'
import { getAuthorizedRouter } from '@/api/router'
import { mapGetters } from 'vuex'

export default {
  data() {
    return {
      displayLoading: false,
      loginForm: {
        username: 'admin',
        password: 'admin'
      },
      rules: {
        username: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
        password: [{ required: true, message: '请输入口令', trigger: 'blur' }]
      }
    };
  },
  computed: {
    ...mapGetters(['showLoading'])
  },
  methods: {
    doLogin() {
      this.$refs['loginForm'].validate(valid => {
        if (valid) {
          this.$store.dispatch('setLoading', true)
          getToken(this.loginForm.username, this.loginForm.password)
            .then(response => {
              sessionStorage.setItem('currentUser', JSON.stringify({
                id: response.data.userId,
                name: response.data.username
              }))
              sessionStorage.setItem('token', response.data.token)
              sessionStorage.setItem('routers', JSON.stringify(response.data.routers))

              // 初始化首页路由
              this.initIndexRouter()

              let redirect = decodeURIComponent(
                this.$route.query.redirect || "/"
              )
              this.$router.push(redirect);
              this.$store.dispatch('setLoading', false)
            })
            .catch(error => {
              this.$store.dispatch('setLoading', false)
              this.$message({
                showClose: true,
                message: error,
                type: 'error'
              })
            })
        }
      })
    },
    initIndexRouter() {
      let indexRouter = {
        path: '/',
        name: "/",
        component: resolve => require(['@/views/home/index'], resolve),
        children: [...this.generateChildRouters()]
      }
      this.$router.addRoutes([indexRouter])
    },
    generateChildRouters() {

      if (!sessionStorage.routers) {
        return []
      }

      let routers = JSON.parse(sessionStorage.routers)
      let childRouters = []
      console.log(routers);
      for(let router of routers) {

        console.log(router);
        if(router.code != null) {
          let routerProps = JSON.parse(router.properties)

          let childRouter = {
            path: router.url,
            name: router.code,
            component: resolve => require(['@/views/' + router.code + '/index'], resolve),
            meta: { routerId: router.id, requiresAuth: routerProps.meta.requiresAuth, nameFullPath: routerProps.nameFullPath }
          }

          childRouters.push(childRouter)
        }
      }

      return childRouters
    }
  }
}
</script>

<style>
  .login-container {
    border-radius: 15px;
    background-clip: padding-box;
    margin: 180px auto;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
  }
  .login_title {
    margin: 0px auto 40px auto;
    text-align: center;
    color: #505458;
  }
  .login_remember {
    margin: 0px 0px 35px 0px;
    text-align: left;
  }
</style>
