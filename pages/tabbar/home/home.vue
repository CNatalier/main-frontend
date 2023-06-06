<template>
	<view class="center">
    
		<view class="logo" :hover-class="!login ? 'logo-hover' : ''">
		  <image class="logo-img" src="../../../static/zaizai-login/login.png"></image>
		  <view class="logo-title">
		    <text class="uer-name">Hi，{{userInfo.username}}</text>
		    <text class="user-phone">手机号：{{userInfo.phone}}</text>
		  </view>
		</view>

		
		<view class="center-list"> 
			<view class="center-list-item">
				<text class="list-icon">&#xe65f;</text>
				<text class="list-text">反馈建议</text>
				<text class="navigat-arrow">&#xe65e;</text>
			</view>
		</view>
    <view class="center-list">
    <view class="center-list-item">
    	<text class="list-icon">&#xe65f;</text>
    	<text class="list-text">服务条款及隐私</text>
    	<text class="navigat-arrow">&#xe65e;</text>
    </view>
    </view>
		<view class="center-list">
			<view class="center-list-item">
				<text class="list-icon">&#xe614;</text>
				<text class="list-text">检查更新</text>
				<text class="navigat-arrow">&#xe65e;</text>
			</view>
		</view>
    <view class="center-list">
        <view class="center-list-item border-bottom" @click="logout">
          <text class="list-icon">&#xe60f;</text>
          <text class="list-text">退出登录</text>
          <text class="navigat-arrow">&#xe65e;</text>
        </view>
      </view>
	</view>
</template>

<script>
	export default {
  data() {
      return { 
        userInfo: {
           
          username: '', // 用户名
          phone: '' // 用户手机
        }
      }
    },
    created() {
        this.getUserInfo()
      },

  methods: {
    // 
        getUserInfo() {
              const storedUserInfo = uni.getStorageSync('userInfo') // 从本地存储获取用户名
              console.log('Stored User Info:', storedUserInfo)
              if(storedUserInfo) {
                this.userInfo = storedUserInfo 
                console.log('Fetched User Info:', this.userInfo)
              } else {
                console.log('No User Info found')
              }
            }, 
       
       onLoad() {
         this.getUserInfo()
       },
       onShow() {
         this.getUserInfo()
       },
    logout() {
      // 显示提示框
      
      uni.showModal({
        title: '提示',
        content: '确定退出登录吗？',
        success: res => {
          if (res.confirm) {
            // 用户点击了"确定"按钮，进行退出登录的逻辑并重定向到登录页面
            console.log('用户点击确定')
            this.login = false // 退出登录
            this.uerInfo = {} // 清空用户信息
            uni.showToast({
              title: '退出成功', 
              icon: 'success',
            }) 
            uni.removeStorageSync('userInfo') // 移除存储的cookie
            uni.navigateTo({ url: '../../zaizai-login/index' })
          } else if (res.cancel) {
            console.log('用户点击取消')
          }
        }
      })
    },
    // ...
  }
  // ...
}
</script>
<style>
	@font-face {
		font-family: texticons;
		font-weight: normal;
		font-style: normal;
		src: url('https://at.alicdn.com/t/font_984210_5cs13ndgqsn.ttf') format('truetype');
	}

	page,
	view {
		display: flex;
	}

	page {
		background-color: #f8f8f8;
	}

	.center {
		flex-direction: column;
	}

	.logo {
		width: 750upx;
		height: 240upx;
		padding: 20upx;
		box-sizing: border-box;
		background-color: #4cd964;
		flex-direction: row;
		align-items: center;
	}

	.logo-hover {
		opacity: 0.8;
	}

	.logo-img {
		width: 150upx;
		height: 150upx;
		border-radius: 150upx;
	}

	.logo-title {
		height: 150upx;
		flex: 1;
		align-items: center;
		justify-content: space-between;
		flex-direction: row;
		margin-left: 20upx;
	}

	.uer-name {
		height: 60upx;
		line-height: 60upx;
		font-size: 38upx;
		color: #FFFFFF;
	}

	.go-login.navigat-arrow {
		font-size: 38upx;
		color: #FFFFFF;
	}

	.login-title {
		height: 150upx;
		align-items: self-start;
		justify-content: center;
		flex-direction: column;
		margin-left: 20upx;
	}

	.center-list {
		background-color: #FFFFFF;
		margin-top: 20upx;
		width: 750upx;
		flex-direction: column;
	}

	.center-list-item {
		height: 90upx;
		width: 750upx;
		box-sizing: border-box;
		flex-direction: row;
		padding: 0upx 20upx;
	}

	.border-bottom {
		border-bottom-width: 1upx;
		border-color: #c8c7cc;
		border-bottom-style: solid;
	}

	.list-icon {
		width: 40upx;
		height: 90upx;
		line-height: 90upx;
		font-size: 34upx;
		color: #4cd964;
		text-align: center;
		font-family: texticons;
		margin-right: 20upx;
	}

	.list-text {
		height: 90upx;
		line-height: 90upx;
		font-size: 34upx;
		color: #555;
		flex: 1;
		text-align: left;
	}

	.navigat-arrow {
		height: 90upx;
		width: 40upx;
		line-height: 90upx;
		font-size: 34upx;
		color: #555;
		text-align: right;
		font-family: texticons;
	}
</style>