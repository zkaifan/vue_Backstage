<template>
  <div class="login_container">
    <div class="login_box">
      <h1>Login</h1>
      <el-form
        class="login_form"
        ref="loginFormRef"
        :model="loginForm"
        :rules="loginFormRules"
      >
        <!-- 用户名 -->
        <el-form-item prop="username">
          <el-input
            prefix-icon="iconfont icon-user"
            v-model="loginForm.username"
          ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item prop="password">
          <el-input
            prefix-icon="iconfont icon-3702mima"
            v-model="loginForm.password"
            type="password"
          ></el-input>
        </el-form-item>
        <!-- 按钮 -->
        <el-form-item class="button">
          <el-button type="primary" @click="Login">登录</el-button>
          <el-button type="info" @click="resetLoginForm">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 登录表单数据绑定
      loginForm: {
        username: "admin",
        password: "123456"
      },
      // 表单验证规则
      loginFormRules: {
        username: [
          {
            required: true,
            message: "请输入用户名",
            trigger: "blur"
          },
          {
            min: 5,
            max: 10,
            message: "长度在 5 到 10 个字符",
            trigger: "blur"
          }
        ],
        password: [
          {
            required: true,
            message: "请输入密码",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    resetLoginForm() {
      // 点击重置按钮，重置表单
      this.$refs.loginFormRef.resetFields();
      this.loginForm.username = "";
      this.loginForm.password = "";
    },
    Login() {
      // 登录预验证
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return;
        const { data: res } = await this.$http.post("login", this.loginForm);
        if (res.meta.status !== 200)
          return this.$message.error("用户名或密码错误");
        this.$message.success("登录成功");
        // 1、将登录成功之后的token，保存到客户端的sessionStorage中
        //    1.1 项目中除了登录之外的其他API接口，必须在登录之后才能访问
        //    1.2 token 只应在当前网站打开期间生效，所以将token保存在sessionStorage
        // 2、通过编程式路由导航跳转到主页后台，地址是/home
        window.sessionStorage.setItem("token", res.data.token);
        this.$router.push("/home");
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.login_container {
  width: 100vw;
  height: 100vh;
  background: #2b4b6b;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  .login_box {
    width: 350px;
    height: 270px;
    background: #fff;
    border-radius: 5px;
    position: relative;
    h1 {
      color: rgba(0, 0, 0, 0.5);
      text-align: center;
      margin: 10px 0;
    }
    .login_form {
      width: 100%;
      padding: 0 25px;
      position: absolute;
      bottom: 0;
      .button {
        display: flex;
        justify-content: flex-end;
      }
    }
  }
}
</style>
