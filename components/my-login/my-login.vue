<template>
	 <view class="login-container">
	    <!-- 提示登录的图标 -->
	    <uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
	    <!-- 登录按钮 -->
	    <button type="primary" class="btn-login" open-type="getUserInfo" @getuserinfo="getUserInfo">一键登录</button>
	    <!-- 登录提示 -->
	    <view class="tips-text">登录后尽享更多权益</view>
	  </view>
</template>

<script>
import { mapMutations, mapState } from 'vuex'
	
	export default {
		name:"my-login",
		computed: {
		    // 调用 mapState 辅助方法，把 m_user 模块中的数据映射到当前用组件中使用
		    ...mapState('m_user', ['redirectInfo']),
		  },
		data() {
			return {
				
			};
		},
		methods:{
    ...mapMutations('m_user', ['updateUserInfo', 'updateToken', 'updateRedirectInfo']),
			getUserInfo(e){
				// 判断是否获取用户信息成功
				    if (e.detail.errMsg === 'getUserInfo:fail auth deny') return uni.$showMsg('您取消了登录授权！')
				
				    // 获取用户信息成功， e.detail.userInfo 就是用户的基本信息
						this.updateUserInfo(e.detail.userInfo)
						this.getToken(e.detail)
			},
			async getToken(info) {
			  // 调用微信登录接口
			  //const [err, res] = await uni.login().catch(err => err)
			  // 判断是否 uni.login() 调用失败
			 // if (err || res.errMsg !== 'login:ok') return uni.$showError('登录失败！')
			
			  // 准备参数对象
			  // const query = {
					// "code": "013pZWCt0GniKb1ftkBt0AxfDt0pZWCf",
					// "encryptedData": "FGMaNbO4ytTFTUzK3wmtfHWoFeQJYkyMajQ5tVbQglwmnJ46SMi43Thq0OZjSSa5Msmnpdfx7yigYkdvUbsiQFyF9xMGEQLpUFcpNjnYoCQOl2Ka5zEeqpMY91ywYAsPISeu1rwvFCU2Ucenxf7gdcDPQ/jNJdORGb86DR+2gVJeg8TZiXWJ66enpynj35DiaE1jNWUSzpgbOvO40dZrWnHVwDEJN+upeSHRR/IW7t3cgtV63/GeZzNhFbUYqNHO6M1KHetwY1u8mFXuZoXwVV7DrEwEzY1btqcfW93q4ekn2uwAUeJM4U+3roBsIT5ZpEM8YtQZVsAwjaWHBe8id1H4lurEFC5/GwLnRONrIiR7KDF7MPxnMO9flWFQEfay5dt9rBELzk1Efjf2H6UnzYfh+o4MbPxtc0cUiHEnNCVcXjDtulXSRUy7RZQPmjF/tYBHHrXEFubxnK4oRBZz/A==",
					// "iv": "y/ESFBaesbEzIbB1th5Knw==",
					// "rawData":
					// 	"{\"nickName\":\"优购\",\"gender\":0,\"language\":\"zh_CN\",\"city\":\"\",\"province\":\"\",\"country\":\"\",\"avatarUrl\":\"https://wx.qlogo.cn/mmopen/vi_32/icWlxE4rARHaIlib1PRmBtRa2tQicUSEHYu8UIGZ0LLic9C0PticibED6brRFCuQYeLGtwTcBYFgMtcF11N31pVhMF8g/132\"}",
					// "signature": "d06cd3a54e89e6014e43694844706eaccad109bb"
			  // }
			
			  // 换取 token
			  // const { data: loginResult } = await uni.$http.post('/api/public/v1/users/wxlogin', query)
			  // if (loginResult.meta.status !== 200) return uni.$showMsg('登录失败！')
			  uni.$showMsg('登录成功')
				this.updateToken('Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEyLCJpYXQiOjE1MjU0MDIyMjMsImV4cCI6MTUyNTQ4ODYyM30.g-4GtEQNPwT_Xs0Pq7Lrco_9DfHQQsBiOKZerkO-O-o')
				this.navigateBack()
			},
			// 返回登录之前的页面
			navigateBack() {
			  // redirectInfo 不为 null，并且导航方式为 switchTab
			  if (this.redirectInfo && this.redirectInfo.openType === 'switchTab') {
			    // 调用小程序提供的 uni.switchTab() API 进行页面的导航
			    uni.switchTab({
			      // 要导航到的页面地址
			      url: this.redirectInfo.from,
			      // 导航成功之后，把 vuex 中的 redirectInfo 对象重置为 null
			      complete: () => {
			        this.updateRedirectInfo(null)
			      }
			    })
			  }
			}
		}
	}
</script>

<style lang="scss">
.login-container {
  // 登录盒子的样式
  height: 750rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #f8f8f8;
  position: relative;
  overflow: hidden;

  // 绘制登录盒子底部的半椭圆造型
  &::after {
    content: ' ';
    display: block;
    position: absolute;
    width: 100%;
    height: 40px;
    left: 0;
    bottom: 0;
    background-color: white;
    border-radius: 100%;
    transform: translateY(50%);
  }

  // 登录按钮的样式
  .btn-login {
    width: 90%;
    border-radius: 100px;
    margin: 15px 0;
    background-color: #c00000;
  }

  // 按钮下方提示消息的样式
  .tips-text {
    font-size: 12px;
    color: gray;
  }
}
</style>