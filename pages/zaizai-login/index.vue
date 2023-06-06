<template>
	<view class="zai-box">
		<image src="../../static/zaizai-login/login.png" mode='aspectFit' class="zai-logo"></image>
		<view class="zai-title"></view>
		<view class="zai-form">
			 <input class="zai-input" v-model="username" placeholder-class placeholder="请输入用户名" />
			  <input class="zai-input" v-model="password" placeholder-class password placeholder="请输入密码" />
			  <view class="zai-label">忘记密码？</view>
			  <button class="zai-btn" @click="login">立即登录</button>
			<navigator url="../zaizai-register/index" hover-class="none" class="zai-label">还没有账号？点此注册.</navigator>
		</view>
	</view>
</template>

<script>
export default {
  data() {
    return {
      username: '', // 存储用户名
      password: '' // 存储密码
    }
  },
  methods: {
    login() {
      let systemInfo = uni.getSystemInfoSync()
      console.log(systemInfo.platform)
      const self = this
      // 调用登录接口
uni.request({
        url: 'http://123.60.11.207:80/login',
        method: 'POST',
        header: { 'content-type': 'application/json' },
        data: {
          username: this.username,
          pwd: this.password
        },
        success: function (res) {
          console.log(res)
          if (res.statusCode === 200) {
            const data = res.data
            switch (data.code) {
              case 200:
                // 登录成功
                uni.showToast({
                  title: '登录成功',
                  icon: 'success',
                }) 
                // 获取并保存Cookie
                const cookie = res.header['Set-Cookie'] || res.header['set-cookie']
                uni.setStorageSync('cookie', cookie)
                uni.setStorageSync('username', self.username) // 存储用户名
                self.fetchUserInfo(this.username)
                
                console.log('9999999999999')
                
                

                 
                break
              case -1:
              case 10001:
                // 登录失败
                uni.showToast({
                  title: data.message,
                  icon: 'none'
                })
                break
              default:
                // 其他错误
                uni.showToast({
                  title: '发生未知错误',
                  icon: 'none'
                })
                break
            }
          } else {
            // 请求失败
            uni.showToast({
              title: '请求失败',
              icon: 'none'
            })
          }
        },
        fail: function (err) {
          // 处理请求失败
          uni.showToast({
            title: '网络请求失败',
            icon: 'none'
          })
        }
      })

    },
    fetchUserInfo(username) {
      uni.request({
        url: 'http://123.60.11.207:80/userinfo',
        method: 'GET',
        data: { username: username },
        success: res => {
          if (res.data.code === 200) {
            uni.setStorageSync('userInfo', res.data.data) // 存储用户信息
            console.log(res.data.data)
            console.log(1)
            uni.switchTab({ url: '../tabbar/gogogo/gogogo' })
            console.log(2)
          } else {
          }
        }
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
	}
	.zai-btn:after{
		border: 0;
	}
	/*按钮点击效果*/
	.zai-btn.button-hover{
		transform: translate(1upx, 1upx);
	}
</style>
