<template>
  <div class="login-container">
    <el-form ref="formInline" size="mini" :model="formInline" :rules="ruleInline" class="login-form" autocomplete="on" label-position="left">
      <div class="title-container">
        <h3 class="title">短信账户注册</h3>
      </div>
      <el-form-item prop="account">
        <el-input
          ref="account"
          v-model="formInline.account"
          placeholder="请输入短信平台账号"
          prefix-icon="el-icon-user"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="off"
        />
      </el-form-item>
      <el-form-item prop="password">
        <el-input
          :key="passwordType"
          ref="password"
          v-model="formInline.password"
          :type="passwordType"
          placeholder="请输入短信平台密码/token"
          name="password"
          tabindex="2"
          auto-complete="off"
          prefix-icon="el-icon-lock"
        />
        <span class="show-pwd" @click="showPwd">
          <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
        </span>
      </el-form-item>
      <el-form-item prop="url">
        <el-input
          ref="password"
          v-model="formInline.url"
          placeholder="请输入网址域名"
          name="password"
          prefix-icon="el-icon-position"
        />
      </el-form-item>
      <el-form-item prop="phone">
        <el-input
          ref="password"
          v-model="formInline.phone"
          placeholder="请输入您的手机号"
          prefix-icon="el-icon-phone-outline"
          name="password"
        />
      </el-form-item>
      <el-form-item prop="sign">
        <el-input
          ref="password"
          v-model="formInline.sign"
          placeholder="请输入短信签名，例如：CRMEB"
          name="password"
          prefix-icon="el-icon-price-tag"
        />
      </el-form-item>
      <el-form-item prop="code" class="captcha">
        <div class="acea-row" style="flex-wrap: nowrap;">
          <el-input
            ref="username"
            v-model="formInline.code"
            placeholder="验证码"
            name="username"
            type="text"
            tabindex="1"
            autocomplete="off"
            prefix-icon="el-icon-message"
            style="width: 90%"
          />
          <el-button size="small" :disabled=!this.canClick @click="cutDown">{{cutNUm}}</el-button>
        </div>
      </el-form-item>
      <el-button :loading="loading" type="primary" style="width:100%;margin-bottom:20px;" @click="handleSubmit('formInline')">注册</el-button>
      <el-button type="primary" style="width:100%;margin-bottom:20px;" @click="changelogo">立即登录</el-button>
    </el-form>
  </div>
</template>

<script>
// +----------------------------------------------------------------------
// | CRMEB [ CRMEB赋能开发者，助力企业发展 ]
// +----------------------------------------------------------------------
// | Copyright (c) 2016~2021 https://www.crmeb.com All rights reserved.
// +----------------------------------------------------------------------
// | Licensed CRMEB并不是自由软件，未经许可不能去掉CRMEB相关版权
// +----------------------------------------------------------------------
// | Author: CRMEB Team <admin@crmeb.com>
// +----------------------------------------------------------------------
import { registerApi } from '@/api/sms'
import { captchaApi } from '@/api/setting'
export default {
  name: 'Register',
  data() {
    const validatePhone = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请填写手机号'))
      } else if (!/^1[3456789]\d{9}$/.test(value)) {
        callback(new Error('手机号格式不正确!'))
      } else {
        callback()
      }
    }
    return {
      loading: false,
      passwordType: 'password',
      captchatImg: '',
      cutNUm: '获取验证码',
      canClick: true,
      formInline: {
        account: '',
        code: '',
        url: '',
        phone: '',
        sign: '',
        password: ''
      },
      ruleInline: {
        account: [
          { required: true, message: '请输入短信平台账号', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入短信平台密码/token', trigger: 'blur' }
        ],
        url: [
          { required: true, message: '请输入网址域名', trigger: 'blur' }
        ],
        phone: [
          { required: true, validator: validatePhone, trigger: 'blur' }
        ],
        sign: [
          { required: true, message: '请输入短信签名', trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入验证码', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    showPwd() {
      if (this.passwordType === 'password') {
        this.passwordType = ''
      } else {
        this.passwordType = 'password'
      }
      this.$nextTick(() => {
        this.$refs.password.focus()
      })
    },
    // 短信验证码
    cutDown() {
      if (this.formInline.phone) {
        if (!this.canClick) return
        this.canClick = false
        this.cutNUm = 60
        captchaApi(this.formInline.phone).then(async res => {
          this.$message.success(res.message)
        }).catch(res => {
          this.$message.error(res.message)
        })
        const time = setInterval(() => {
          this.cutNUm--
          if (this.cutNUm === 0) {
            this.cutNUm = '获取验证码'
            this.canClick = true
            clearInterval(time)
          }
        }, 1000)
      } else {
        this.$message.warning('请填写手机号!')
      }
    },
    // 注册
    handleSubmit(name) {
      this.$refs[name].validate((valid) => {
        if (valid) {
          registerApi(this.formInline).then(async res => {
            // this.$message.success(res.message)
            // setTimeout(() => {
            //   this.changelogo()
            // }, 1000)
            this.$alert(res.message, '提示', {
                confirmButtonText: '确定',
                callback: action => {
                    this.changelogo()
                }
            });
          }).catch(res => {
            this.$message.error(res.message)
          })
        } else {
          return false
        }
      })
    },
    // 立即登录
    changelogo() {
      this.$emit('on-change')
    }
  }
}
</script>
<style lang="scss" scoped>
  .el-button+.el-button{
    margin-left: 0px !important;
  }
  .title{
    text-align: center;
  }
  .captcha{
    display: flex;
    align-items: flex-start;
    /deep/.el-form-item__content{
      width: 100%;
    }
  }
  $bg: #2d3a4b;
  $dark_gray: #889aa4;
  $light_gray: #eee;
  .imgs{
    img{
      height: 36px;
    }
  }
  .login-form {
    flex: 1;
    padding: 32px 0;
    text-align: center;
    width: 384px;
    margin: 0 auto;
    overflow: hidden;
  }
  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }
  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }
  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
    /deep/.svg-icon {
      vertical-align: 0.3em;
    }
  }
  .thirdparty-button {
    position: absolute;
    right: 0;
    bottom: 6px;
  }
</style>
