<template>
  <div class="d-login">
    <div class="d-loginheader">
      <img class="img" src="../assets/loginTitle.png">
      <span class="title">信息考评管理系统</span>
    </div>
    <div class="login-content">
      <p class="tip">账户密码登录</p>
      <el-form ref="loginData" :model="loginData" :rules="rulesForm">
        <el-form-item prop="username">
          <el-input v-model="loginData.username" style="width: 300px;" placeholder="用户名"></el-input>
        </el-form-item>
        <el-form-item prop="password" class="password_font_algin">
          <el-input
            type="password"
            v-model="loginData.password"
            style="width: 300px;"
            placeholder="密码"
          ></el-input>
        </el-form-item>
        <el-form-item style="text-align:center;">
          <el-button
            type="primary"
            plain
            size="medium"
            style="width: 300px;"
            @click="submit('loginData')"
          >登&nbsp;&nbsp;录</el-button>
        </el-form-item>
      </el-form>
    </div>
    <!-- <div class="footer">
      <p>© 2005-2018 勤智数码科技股份有限公司 蜀 ICP 备 11012966 号</p>
    </div>-->
  </div>
</template>
<style lang="less" scoped>
@import "../css/login.less";
</style>

<script>
import md5 from "md5";
export default {
  data() {
    return {
      rulesForm: {
        username: [
          { required: true, message: "用户名不能为空!", trigger: "change" }
        ],
        password: [
          { required: true, message: "密码不能为空!", trigger: "change" }
        ]
      },
      loginData: {
        username: "",
        password: ""
      }
    };
  },
  created() {
    // 若有什么统一登录，在此设置
    const token = this.getToken("token"); // 获取统一登录的某个标识
    if (token) {
      sessionStorage.setItem("token", token);
      this.$store.commit("changeLogin");
      if (token) {
        this.$get("/user/loginUserInfo", null, data => {
          sessionStorage.setItem("user", JSON.stringify(data.object));
          this.$store.commit("changeLogin");
          this.$router.push({ path: "/" });
        });
      }
    }
  },
  methods: {
    getToken(name) {
      let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
      let r = window.location.search.substr(1).match(reg);
      if (r != null) return decodeURI(r[2]);
      return null;
    },
    submit(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          const postData = {
            username: this.loginData.username,
            password: md5(this.loginData.password)
          };
          this.$post("/data/login", postData, data => {
            sessionStorage.setItem("token", data.object.tokenMap.accessToken);
            sessionStorage.setItem("user", JSON.stringify(data.object.user));
            this.$store.commit("changeLogin");
            this.$router.push({ path: "/" });
          });
        }
      });
    }
  }
};
</script>
