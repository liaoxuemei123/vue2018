<template>
  <div class="page-container">
    <div class="login-page page" flex="dir:top box:first">
      <nav-bar title="长安通行证登录" :onRight="register" rightContent="注册" rightIcon="space" />
      <div class="page-content" :style="{'background-image':'url('+require('../assets/bg.png')+')'}">
        <!-- <div class="person-picture" :style="{'background-image':'url('+require('../assets/person-picture.png')+')'}"></div> -->
        <div class="bisiness-list">
          <xscroller>
            <div class="bisiness-item x-item" v-tap="changeActive.bind(this,index)" :class="{'active':activeIndex == index}" v-for="(item,index) in loginOptions" :key="index" flex="dir:left cross:center main:center" style="font-size:15px;">
              {{item.name}}
            </div>
          </xscroller>
        </div>
        <div class="tab-content">
          <transition :name="mode">
            <!-- <keep-alive> -->
            <div class="tra-div" v-if="activeIndex==0" key="0">
              <div class="form-group">
                <div class="inp-com" flex="dir:left box:first cross:center" style="border-bottom:1px solid #e0e0e0">
                  <div class="input-label">
                    <span>手机号</span>
                  </div>
                  <div flex="dir:left box:last cross:center">
                    <input type="tel" maxlength="11" placeholder="手机号" v-model="tel">
                    <i class="iconfont icon-close" v-if="iconShow" @click="iconClick( 'tel')"></i>
                  </div>
                </div>
              </div>
              <div class=" form-group">
                <div class="inp-com" flex="dir:left box:first cross:center">
                  <div class="input-label">
                    <span>短信验证码</span>
                  </div>
                  <div flex="dir:left box:last cross:center">
                    <input type="text" maxlength="6" placeholder="短信验证码" v-model="code">
                    <button class="retry btn-xs" @click="sendCode" :disabled="btnDisabled" :class="{ 'btnDis':!btnDisabled}">{{codeMessage}}</button>
                  </div>
                </div>
              </div>
              <div style="padding: 20px 10px 16px 10px;">
                <div class="login-btn" @click="loginCode">登录</div>
              </div>
            </div>
            <div class="tra-div" v-else key="1">

              <div class="form-group">
                <div class="inp-com" flex="dir:left box:first cross:center" style="border-bottom:1px solid #e0e0e0">
                  <div class="input-label">
                    <span>手机号</span>
                  </div>
                  <div flex="dir:left box:last cross:center">
                    <input type="tel" maxlength="11" placeholder="手机号" v-model="tel">
                    <i class="iconfont icon-close" v-if="iconShow" @click="iconClick( 'tel')"></i>
                  </div>
                </div>
              </div>
              <div class="form-group">
                <div class="inp-com" flex="dir:left box:first cross:center">
                  <div class="input-label">
                    <span>密码</span>
                  </div>
                  <div flex="dir:left box:last cross:center">
                    <input type="password" maxlength="16" placeholder="密码" v-model="password">
                    <i class="iconfont icon-close" v-if="iconPwdShow" @click="iconClick( 'password')"></i>
                  </div>
                </div>
              </div>
              <div style="padding: 20px 10px 16px 10px;">
                <div class="login-btn" @click="loginPwd">登录</div>
              </div>
              <transition name="slide-up">
                <p class="tips" v-if="forgetPassword">
                  <a href="https://cloud.mall.changan.com.cn/caecapp/main/index.html#my/forget-password.html">忘记密码?</a>
                </p>
              </transition>
            </div>
            <!-- </keep-alive> -->
          </transition>
        </div>

      </div>
    </div>
  </div>
</template>
<script>
import NavBar from "../components/NavBar";
import InpCom from "../components/InpCom";
import Tool from "../utils/Tool";
import En from "../utils/Encryption";
import Xscroller from "../components/Xscroller";
import { Toast } from "mint-ui";
import errorMsg from "../utils/error_msg";
export default {
  data() {
    return {
      tel: "",
      password: "",
      code: "",
      btnDisabled: true,
      forgetPassword: false,
      activeIndex: 0,
      mode: "left",
      iconPwdShow: false,
      iconShow: false,
      codeMessage: "发送验证码",
      regexc: /^((0\d{2,3}-\d{7,8})|(1[3-9]\d{9}))$/,
      loginOptions: [{ name: "快捷登录", index: 0 }, { name: "账户密码登录", index: 1 }],
      num: 60,
      timer: null //计时器
    };
  },
  components: {
    NavBar,
    Xscroller,
    InpCom
  },
  watch: {
    tel(val) {
      this.iconShow = val.length > 0 ? true : false;
      this.btnDisabled = true;
      if (val.length == 11) {
        if (this.regexc.test(val)) {
          this.btnDisabled = false;
        } else {
          Toast({
            duration: 1000,
            message: "手机号码格式不正确哦！"
          });
        }
      }
    },
    password(val) {
      this.iconPwdShow = val.length > 0 ? true : false;
    }
  },
  methods: {
    sendCode() {
      if (this.regexc.test(this.tel)) {
        this.num = 60;
        var that = this;
        Tool.post("wx/getSMSCode", { phone: this.tel }, data => {
          console.log(data);
          if (data.result == 0) {
            Toast({
              duration: 1000,
              message: "获取验证码成功"
            });
            that.btnDisabled = true;
            this.codeMessage = "重新发送(" + that.num + ")";
            this.timer = setInterval(function() {
              that.num--;
              console.log(that.num == 0);
              if (that.num == 0) {
                that.codeMessage = "再次发送";
                that.btnDisabled = false;
                clearInterval(that.timer);
                return false;
              }
              that.codeMessage = "重新发送(" + that.num + ")";
            }, 1000);
          } else {
            Toast({
              duration: 1000,
              message: data.msg
            });
            this.btnDisabled = false;
          }
        });
        // 接口
      } else {
        Toast({
          duration: 1000,
          message: "手机号码格式不正确哦！"
        });
        this.btnDisabled = true;
      }
    },
    changeActive(index) {
      this.activeIndex = index;
      this.mode = this.activeIndex == 0 ? "right" : "left";
      console.log(this.mode);
    },
    iconClick(val) {
      this.$data[val] = ""; //置空
    },
    updateTel: function(e) {
      this.tel = $(e.target).val();
    },
    updatePwd: function(e) {
      this.password = $(e.target).val();
    },
    updateCode: function(e) {
      this.code = $(e.target).val();
    },
    register: function() {
      this.$router.push({ name: "register", params: this.$route.params });
    },
    loginCode() {
      var self = this;
      if (!this.tel) {
        Toast({
          duration: 1000,
          message: "手机号码不能为空！"
        });
        return false;
      }
      if (!this.regexc.test(this.tel)) {
        Toast({
          duration: 1000,
          message: "手机号码格式不正确哦！"
        });
        return false;
      }
      if (this.codeMessage == "发送验证码") {
        Toast({
          duration: 1000,
          message: "请发送验证码！"
        });
        return false;
      }
      if (!this.code) {
        Toast({
          duration: 1000,
          message: "验证码不能为空哦！"
        });
        return false;
      }
      Tool.post("wx/loginByCode", { mobile: this.tel, code: this.code }, data => {
        if (data.success) {
          if (data.data.result != "0") {
            Toast({
              duration: 1000,
              message: errorMsg[data.data.result]
            });
          } else {
            Toast({
              duration: 1000,
              message: "登录成功"
            });
            // 登陆成功之后去掉页面倒计时还原所有状态
          }
          // self.$router.go(-2);
          Tool.localItem("userInfo", data.data);
          this.afterSuccess();
          Tool.removeLocalItem("oid");
          if (self.$route.params.to) {
            if (self.$route.params.preCtoken) {
              self.$router.push({
                path: self.$route.params.to,
                query: { token: data.data.token }
              });
            } else self.$router.push({ path: self.$route.params.to });
          } else {
            self.$router.push({ name: "maintainset" });
          }
        } else {
          Toast({
            duration: 1000,
            message: "短信验证码验证失败"
          });
          self.forgetPassword = true;
        }
      });
    },
    afterSuccess() {
      clearInterval(this.timer);
      this.num = 60;
      this.codeMessage = "发送验证码";
      this.activeIndex = 0;
      if (this.tel.length == 11) {
        this.btnDisabled = false;
      } else this.btnDisabled = true;
    },
    loginPwd: function() {
      this.tel = this.tel.replace(/\s/g, "");
      console.log(this.tel);
      if (!this.tel) {
        Toast({
          duration: 1000,
          message: "手机号码不能为空！"
        });
        return false;
      }
      if (!this.password) {
        Toast({
          duration: 1000,
          message: "密码不能为空！"
        });
        return false;
      }
      if (!this.regexc.test(this.tel)) {
        Toast({
          duration: 1000,
          message: "手机号码格式不正确哦！"
        });
        return false;
      }
      var self = this;
      En.createPassword(this.password).then(pData => {
        Tool.post(
          "loginCode",
          {
            mobile: this.tel,
            password: pData.password,
            mod: pData.mod,
            additional: pData.additional
          },
          data => {
            if (data.success) {
              if (data.data.result != "0") {
                Toast({
                  duration: 1000,
                  message: errorMsg[data.data.result]
                });
              } else {
                Toast({
                  duration: 1000,
                  message: "登录成功"
                });
              }
              // self.$router.go(-2);
              Tool.localItem("userInfo", data.data);
              this.afterSuccess();
              Tool.removeLocalItem("oid");
              if (self.$route.params.to) {
                if (self.$route.params.preCtoken) {
                  self.$router.push({
                    path: self.$route.params.to,
                    query: { token: data.data.token }
                  });
                } else self.$router.push({ path: self.$route.params.to });
              } else {
                self.$router.push({ name: "maintainset" });
              }
            } else {
              Toast({
                duration: 1000,
                message: "账号密码错误"
              });
              self.forgetPassword = true;
            }
          }
        );
      });
    }
  },
  activated: function() {
    this.forgetPassword = true;
    this.activeIndex = 0; //默认快捷登录
  },
  deactivated: function() {
    this.forgetPassword = false;
  },
  beforeRouteEnter: (to, from, next) => {
    next();
  }
};
</script>

 <style lang="less" scoped>
.page {
  height: 100%;
  position: absolute;
  width: 100%;
  .page-content {
    height: 100%;
    overflow: auto;
    background-position: center center;
    background-size: 100% 100%;

    .bisiness-list {
      width: 100%;
      height: 1.5rem;
      line-height: 1.5rem;
      background-color: #fff;
      .bisiness-item {
        width: 50%;
        float: left;
        color: #333;
        &.active {
          color: #00bffe;
        }
        .icon-image {
          width: 1.1rem;
          height: 1.2rem;
          background-size: cover;
        }
      }
    }
    .person-picture {
      /*border:solid;*/
      height: 1.546rem;
      background-repeat: no-repeat;
      background-size: 10%;
      background-position: center center;
      margin-top: 20px;
    }
    .tab-content {
      position: relative;
      .tra-div {
        position: absolute;
        width: 100%;
        height: 100%;
        .form-group {
          // margin-left: 15%;
          // margin-right: 15%;
          padding-left: 20px;
          margin-top: 0.386rem;
          background: transparent;
          display: flex;
          flex-direction: row;
          align-items: center;
          .inp-com {
            height: 2.5rem;
            line-height: 2.5rem;
            background-color: #fff;
            width: 100%;
            .input-label {
              width: 30%;
            }
            input {
              border: none;
              height: 100%;
              width: 90%;
              background-color: transparent;
              font-size: 0.62rem;
              padding: 0.2rem 10% 0.2rem 0%;
              outline: none;
            }
            .retry {
              border: none;
              margin-right: 10px;
              padding: 8px 0px;
              color: #878787;
              width: 88px;
              border-radius: 3px;
              font-size: 0.58rem;
              background-color: #e5e5e5;
            }
            .btnDis {
              border: 1px solid #999;
              // background-color: #f5f5f5;
              background-color: white;
            }
            span {
              font-size: 0.68rem;
              color: #333;
            }
            i.iconfont {
              font-size: 0.68rem;
              margin: 0 0.2rem 0 0.32rem;
              color: #999;
              margin-right: 10px;
            }
          }
          .name-picture,
          .pwd-picture {
            height: 1rem;
            margin-left: 5%;
            margin-right: 5%;
          }
          .user-name,
          .user-pwd {
            width: 80%;
            border: none;
            padding-left: 5%;
            outline: none;
            background: transparent;
          }
        }
      }
    }

    .login-btn {
      height: 2rem;
      width: 100%;
      // margin-left: 15%;
      // margin-right: 15%;
      font-size: 0.7rem;
      border-radius: 0.2rem;
      border: 1px solid #03a9f4;
      background-color: #03a9f4;
      text-align: center;
      line-height: 2rem;
      color: white;
    }
    .register-btn {
      .login-btn;
      border: 1px solid red;
      background-color: red;
    }
    .tips {
      color: #03a9f4;
      margin: 0 10px;
      text-align: right;
      a {
        color: #949494;
        font-size: 0.62rem;
        text-decoration: none;
      }
    }
  }
}
</style>
