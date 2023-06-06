<template>
  <view class="zai-box">
    <image src="../../static/zaizai-login/register.png" mode='aspectFit' class="zai-logo"></image>
    <view class="zai-title"></view>
    <view class="zai-form">
      <input class="zai-input" v-model="username" placeholder="请输入用户名" />
      <input class="zai-input" type="password" v-model="password" placeholder="请输入密码"/>
      <input class="zai-input" type="password" v-model="password2" placeholder="请再输入一次密码"/>
      <input class="zai-input" v-model="phone" placeholder="请输入手机号"/>
      <button class="zai-btn" @click="register">立即注册</button>
      <navigator url="../zaizai-login/index" open-type='navigateBack' hover-class="none" class="zai-label">已有账号，点此去登录.</navigator>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: '',
      password2: '',
      phone: ''
    }
  },
  methods: {
register() {
  if (this.password !== this.password2) {
    uni.showToast({
      title: '两次输入的密码不一致',
      icon: 'none'
    })
    return
  }

  if (!this.username || !this.password || !this.phone) {
    uni.showToast({
      title: '参数不能为空',
      icon: 'none'
    })
    return
  }

  uni.request({
    url: 'http://123.60.11.207:80/register',
    method: 'POST',
    header: { 'content-type': 'application/json' },
    data: {
      username: this.username,
      pwd: this.password,
      phone: this.phone
    }
  }).then(response => {
    let [error, res] = response
    if (res.data.code === -1) {
      if (res.data.message === '用户名已经存在') {
        uni.showToast({
          title: '用户名已经存在',
          icon: 'none'
        })
      } else if (res.data.message === '电话号码格式不正确') {
        uni.showToast({
          title: '电话号码格式不正确',
          icon: 'none'
        })
      } else if (res.data.message === '参数不能为空！') {
        uni.showToast({
          title: '参数不能为空',
          icon: 'none'
        })
      } else {
        uni.showToast({
          title: '未知错误',
          icon: 'none'
        })
      }
    } else if (res.data.code === 200) {
      uni.showToast({ title: '注册成功', })
      // 注册成功后，进行页面跳转到登录页面
        uni.navigateTo({ url: '../zaizai-login/index' })
    } else {
      uni.showToast({
        title: '未知错误',
        icon: 'none'
      })
    }
  }).catch(error => {
    console.error(error)
    uni.showToast({
      title: '网络请求失败',
      icon: 'none'
    })
  })
}

  }
}
</script>

<style>
	.zai-box{
		padding: 0 100upx;
		position: relative;
	}
	.zai-logo{
		width: 100%;
		width: 100%;
		height: 310upx;
	}
	.zai-title{
		position: absolute;
		top: 0;
		line-height: 360upx;
		font-size: 68upx;
		color: #fff;
		text-align: center;
		width: 100%;
		margin-left: -100upx;
	}
	.zai-form{
		margin-top: 300upx;
	}
	.zai-input{
		background: #e2f5fc;
		margin-top: 30upx;
		border-radius: 100upx;
		padding: 20upx 40upx;
		font-size: 36upx;
	}
	.input-placeholder, .zai-input{
		color: #94afce;
	}
	.zai-label{
		padding: 60upx 0;
		text-align: center;
		font-size: 30upx;
		color: #a7b6d0;
	}
	.zai-btn{
		background: #ff65a3;
		color: #fff;
		border: 0;
		border-radius: 100upx;
		font-size: 36upx;
		margin-top: 60upx;
	}
	.zai-btn:after{
		border: 0;
	}
	/*按钮点击效果*/
	.zai-btn.button-hover{
		transform: translate(1upx, 1upx);
	}
</style>
