<template>
  <div class="login">
    <div class="store_header">
      <div class="store_avatar">
        <img
          src="../../assets/images/avatar_default.png"
          alt="头像"
          width="55"
          height="55"
        />
      </div>
      <div class="store_name">litemall-vue</div>
    </div>

    <md-field-group>
      <md-field
        v-model="account"
        icon="username"
        placeholder="请输入测试账号 user123"
        right-icon="clear-full"
        name="user"
        data-vv-as="帐号"
        @right-click="clearText"
      />

      <md-field
        v-model="password"
        icon="lock"
        placeholder="请输入测试密码 user123"
        :type="visiblePass ? 'text' : 'password'"
        :right-icon="visiblePass ? 'eye-open' : 'eye-close'"
        data-vv-as="密码"
        name="password"
        @right-click="visiblePass = !visiblePass"
      />

      <div class="clearfix">
        <div class="float-l">
          <router-link to="/login/registerGetCode">免费注册</router-link>
        </div>
        <div class="float-r">
          <router-link to="/login/forget">忘记密码</router-link>
        </div>
      </div>

      <van-button
        size="large"
        type="danger"
        :loading="isLogining"
        @click="loginSubmit"
        >登录</van-button
      >
    </md-field-group>

    <div class="text-desc text-center bottom_positon">技术支持: litemall</div>
  </div>
</template>

<script>
import field from '@/components/field/';
import fieldGroup from '@/components/field-group/';

import { authLoginByAccount } from '@/api/api';
import { setLocalStorage } from '@/utils/local-storage';
import { emailReg, mobileReg } from '@/utils/validate';

import { Toast } from 'vant';
import wx from 'weixin-js-sdk';

export default {
  name: 'login-request',
  components: {
    [field.name]: field,
    [fieldGroup.name]: fieldGroup,
    Toast
  },
  data() {
    return {
      account: '',
      password: '',
      visiblePass: false,
      isLogining: false,
      userInfo: {}
    };
  },
  created() {
    wx.onMenuShareTimeline({
      title: '11', // 分享标题
      link: '22', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
      imgUrl: '', // 分享图标
      success: function() {
        // 用户点击了分享后执行的回调函数
      }
    });
    // wx.config({
    //     debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    //     appId: 'wx7655252f0ebc5ba3', // 必填，公众号的唯一标识
    //     timestamp: , // 必填，生成签名的时间戳
    //     nonceStr: '', // 必填，生成签名的随机串
    //     signature: '',// 必填，签名
    //     jsApiList: [] // 必填，需要使用的JS接口列表
    // })
  },

  methods: {
    clearText() {
      this.account = '';
    },

    validate() {},

    login() {
      let loginData = this.getLoginData();
      authLoginByAccount(loginData)
        .then(res => {
          this.userInfo = res.data.data.userInfo;
          setLocalStorage({
            Authorization: res.data.data.token,
            avatar: this.userInfo.avatarUrl,
            nickName: this.userInfo.nickName
          });

          this.routerRedirect();
        })
        .catch(error => {
          Toast.fail(error.data.errmsg);
        });
    },

    loginSubmit() {
      this.isLogining = true;
      try {
        this.validate();
        this.login();
        this.isLogining = false;
      } catch (err) {
        console.log(err.message);
        this.isLogining = false;
      }
    },

    routerRedirect() {
      // const { query } = this.$route;
      // this.$router.replace({
      //   name: query.redirect || 'home',
      //   query: query
      // });
      window.location = '#/user/';
    },

    getLoginData() {
      const password = this.password;
      const account = this.getUserType(this.account);
      return {
        [account]: this.account,
        password: password
      };
    },

    getUserType(account) {
      const accountType = mobileReg.test(account) ? 'mobile' : emailReg.test(account) ? 'email' : 'username';
      return accountType;
    }
  }
};
</script>

<style lang="scss" scoped>
@import '../../assets/scss/mixin';
.login {
  position: relative;
  background-color: #fff;
}
.store_header {
  text-align: center;
  padding: 30px 0;
  .store_avatar img {
    border-radius: 50%;
  }
  .store_name {
    padding-top: 5px;
    font-size: 16px;
  }
}
.register {
  padding-top: 40px;
  color: $font-color-gray;
  a {
    color: $font-color-gray;
  }
  > div {
    width: 50%;
    box-sizing: border-box;
    padding: 0 20px;
  }
  .connect {
    @include one-border(right);
    text-align: right;
  }
}
.bottom_positon {
  position: absolute;
  bottom: 30px;
  width: 100%;
}
</style>
