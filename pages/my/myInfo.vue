<template>
	<view class="myInfo">
		<!-- 公共组件-每个页面必须引入 -->
		<public-module></public-module>
		<f-navbar title="个人中心" fontColor="#fff" :bgColor="PrimaryColor" :scrollTop="scrollTop" navbarType='5'
			:isShowLeft="false" :isShowTransparentTitle="false"></f-navbar>
		<view class="headBox"
			:style="{background:'linear-gradient(to left top,'+PrimaryColor+','+freeSpecsButtonBackground+')',paddingTop:systemInfo.navBarH+'px'}">
			<!-- #ifdef MP -->
			<!-- 登录 -->
			<view class="u-flex u-p-l-30 u-p-r-20 u-p-t-30 u-p-b-30">
				<block v-if="userInfo.token">
					<view class="u-m-r-20">
						<image class="avatar" mode="aspectFill" src="/static/logo.png"></image>
					</view>
					<view class="u-flex-1" @click="onJump('/pages/user/set')">
						<view class="nickName u-flex">
							<view class="name u-m-r-10" v-if="userInfo.nickName">{{userInfo.nickName}}</view>
							<view class="placardVip">超级vip</view>
						</view>
						<!-- <view class="detail" v-if="userInfo.phoneNum">手机号：{{userInfo.phoneNum | phone}}</view> -->
						<!-- <view class="detail" v-else>手机号:未绑定</view> -->
					</view>
				</block>
				<block v-else>
					<view class="u-m-r-20">
						<view class="avatar u-flex" style="justify-content: center;">
							<u-icon name="account-fill" color="#fff" size="30"></u-icon>
						</view>
					</view>
					<view class="u-flex-1" @click="openLogin">
						<view class="u-font-lg" style="color: #fff;font-weight: bold;">请点击登录</view>
						<view class="detail">登录后享受更好的服务体验</view>
					</view>
				</block>
				<view><u-icon name="arrow-right" color="#fff" size="13"></u-icon></view>
			</view>
			<!-- #endif -->
			<!-- #ifndef MP -->
			<!-- 登录 -->
			<view class="u-flex u-p-l-30 u-p-r-20 u-p-t-30 u-p-b-30">
				<block v-if="token">
					<view class="u-m-r-20">
						<image class="avatar" mode="aspectFill" src="/static/woman.svg"></image>
					</view>
					<view class="u-flex-1" @click="onJump('/pages/user/set')">
						<view class="nickName">{{userInfo.name}}</view>
						<!-- <view class="detail" v-if="userInfo.phone">{{userInfo.phone }}</view> -->
						<view class="detail" v-if="userInfo.account">账号：{{userInfo.account }}</view>
						<view class="detail" v-if="userInfo.stNumber">{{userInfo.stNumber }}</view>
						<!-- <view class="detail" v-else>手机号:未绑定</view> -->
					</view>
				</block>
				<block v-else>
					<view class="u-m-r-20">
						<view class="avatar u-flex" style="justify-content: center;">
							<u-icon name="account-fill" color="#fff" size="30"></u-icon>
						</view>
					</view>
					<view class="u-flex-1" @click="openLogin">
						<view class="u-font-lg" style="color: #fff;font-weight: bold;">登录 / 注册</view>
						<view class="detail">登录后享受更好的服务体验</view>
					</view>
				</block>
				<view><u-icon name="arrow-right" color="#fff" size="13"></u-icon></view>
			</view>
			<!-- #endif -->
			<!-- 会员卡 -->
			<!--   <view class="vipBox">
                <view class="card">
                    <view class="left">
                        <view class="title">VIP</view>
                        <view class="tips" v-if="isVIP">尊贵的会员，您好！</view>
                        <view class="tips" v-else>成为会员，享受更好的服务体验~</view>
                    </view>
                    <view class="right">
                        <view class="button" v-if="isVIP">会员中心</view>
                        <view class="button" v-else>成为会员</view>
                    </view>
                </view>
            </view> -->
		</view>
		<view class="main">
			<view class="itemBox">
				<view class="titleBox u-flex">
					<view class="title u-flex-m">我的内容</view>
				</view>
				<u-grid :col="4" :border="false">
					<u-grid-item v-for="item in order" :key="item.key"
						@click="onTokenJump(`/pages/my/${item.key}`, item)">
						<view v-if="isRegShow(item)" class="u-flex u-p-t-30 u-p-b-30" style="flex-direction: column;justify-content: center;">
							<image style="width: 50rpx;height: 50rpx;" :src="item.icon" />
							<view class="grid-text">{{item.word}}</view>
						</view>
					</u-grid-item>
				</u-grid>
			</view>
			<view class="itemBox">
				<view class="titleBox u-flex" style="border: none;padding-bottom: 0;">
					<view class="title">功能与服务</view>
				</view>
				<u-grid :col="4" :border="false">
					<u-grid-item v-for="(item,index) in moreFun" :key="index" @click="navClick(item.onPlate)">
						<view class="u-flex u-p-t-30 u-p-b-30"
							style="position: relative;flex-direction: column;justify-content: center;">
							<image style="width: 70rpx;height: 70rpx;" :src="item.icon" />
							<view class="grid-text" style="color: #666;font-size: 22rpx;">{{item.word}}</view>
							<!-- #ifdef MP-WEIXIN -->
							<button style="opacity: 0;width: 100%;height: 100%;position: absolute;left: 0;top: 0;"
								:open-type="item.word=='客服帮助'?'contact':''"></button>
							<!-- #endif -->
						</view>
					</u-grid-item>
				</u-grid>
			</view>
		</view>

		<f-tabbar></f-tabbar>
	</view>
</template>

<script>
	import {
		mapState,
		mapMutations
	} from 'vuex';
	import base from '@/config/baseUrl';
	import fNavbar from '@/components/module/f-navbar/f-navbar';
	import fTabbar from '@/components/module/f-tabbar/f-tabbar';
	export default {
		components: {
			fNavbar,
			fTabbar
		},
		computed: {
			...mapState(['PrimaryColor', 'userInfo', "token"]),
			freeSpecsButtonBackground() {
				return this.$u.colorToRgba(this.$u.rgbToHex(this.PrimaryColor), 0.75)
			},
			
		},
		data() {
			return {
				isHiddenReg: false,
				baseUrl: base.baseUrl,
				systemInfo: base.systemInfo,
				scrollTop: 0,
				isVIP: false,
				// 订单
				order: [{
						icon: '/static/my-course.png',
						key: 'myCourse',
						word: '我的课程',
					},
					//         }, {
					//             icon: '/static/my-favorite.png',
					// key: 'myFavorite',
					//             word: '我的收藏'
					//         }, {
					//             icon: '/static/my-work.png',
					// key: 'myWork',
					//             word: '我的作业'
					//         }, {
					{
						icon: '/static/my-order.png',
						key: 'myOrder',
						word: '我的订单'
					},
					{
						icon: '/static/my-favorite.png',
						key: 'register',
						word: '注册学籍'
					}

				],
				moreFun: [
					// {
					// 	icon: '/static/funMoreIcon3.png',
					// 	word: '客服帮助',
					// 	onPlate: 'onHelp',
					// },
					// {
					// 	icon: '/static/funMoreIcon4.png',
					// 	word: '意见反馈',
					// 	onPlate: 'goFeedback',
					// }, 
					{
						icon: '/static/funMoreIcon5.png',
						word: '关于我们',
						onPlate: 'goAbout',
					}
				],
			}
		},
		onShow(){
			this.getShowReg()
		},
		onLoad() {
			// 隐藏原生的tabbar
			uni.hideTabBar();
			this.getShowReg();
		},
		methods: {
			...mapMutations(['setLoginPopupShow']),
			onJump(url) {
				uni.navigateTo({
					url: url
				})
			},
			isRegShow(item) {
				if(item.key==="register") {
					return !this.isHiddenReg
				} else {
					return true
				}
				
			},
			getShowReg() {
				uni.$u.http.post('/user/info/hasStudentNum', {

				}, {
					custom: {
						load: false,
						auth: true
					}
				}).then((res) => {
					this.isHiddenReg = res
				}).catch((err) => {
					//联网失败, 结束加载
					console.log(err)
				})
			},

			// 跳转前判断登录
			onTokenJump(url, item) {
				// uni.navigateTo({
				// 	url: url
				// });
				if (item && item.key === "register") {
					uni.navigateTo({
						url: '/pages/registration/registration'
					})
					return
				}
				console.log("url",url);
				this.judgeLogin(() => {
					try {
						uni.navigateTo({
							url: url
						});
					} catch (e) {

						//TODO handle the exception
						console.log(e, "error..........")
					}

				});
			},
			openLogin() {
				this.judgeLogin()
			},
			navClick(e) {
				var url = ''
				if (e == 'goMyAddressList') {
					this.$u.toast('您点击了收货地址~')
				} else if (e == 'goCashCouponList') {
					this.$u.toast('您点击了我的优惠券~')
				} else if (e == 'onHelp') {
					// #ifndef MP-WEIXIN
					console.log('小程序客服~')
					// #endif
				} else if (e == 'goFeedback') {
					this.$u.toast('您点击了意见反馈~')
				} else if (e == 'goAbout') {
					uni.navigateTo({
						url: '/pages/webview/webview?url=http://www.pengfkt.com/#/article'
					})
				}
				// url!='' && this.onJump(url)
			},
		},
		onPageScroll(e) {
			this.scrollTop = e.scrollTop;
		},
	}
</script>

<style lang="scss" scoped>
	.myInfo {
		// 这里设置高度，上拉显示菜单栏---正式环境删除
		min-height: 2000rpx;
	}

	.headBox {
		padding-top: 128rpx;
		background: linear-gradient(to left top, #f32735, #fc674d);
		border-radius: 50% / 0 0 5% 5%;
		overflow: hidden;

		.avatar {
			width: 50px;
			height: 50px;
			border-radius: 25px;
			background-color: #ccc;
		}

		.nickName {
			color: #fff;

			.btn {
				font-size: 22rpx;
				font-weight: normal;
				color: #666;
				background: #fff;
				border-radius: 5rpx;
				height: 45rpx;
				line-height: 45rpx;
				padding: 0 10rpx;
				position: relative;

				.itemButton {
					border-radius: 0;
					text-align: left;
					opacity: 0;
					width: 100%;
					height: 100%;
					position: absolute;
					left: 0;
					top: 0;
				}
			}

			.name {
				color: #fff;
				font-weight: bold;
				font-size: 32rpx;
			}

			.placardVip {
				background: #2a2e44;
				color: #f4d6a1;
				font-size: 22rpx;
				padding: 4rpx 10rpx;
				text-align: center;
				border-radius: 4rpx;
			}

		}

		.detail {
			color: #fff;
			font-size: 24rpx;
			padding-top: 6rpx;
		}

		.vipBox {
			padding: 0 24rpx;

			.card {
				background-image: linear-gradient(to right, #314177, #202646);
				padding: 16rpx 32rpx 24rpx 32rpx;
				border-top-left-radius: 30rpx;
				border-top-right-radius: 30rpx;
				display: flex;
				flex-direction: row;
				justify-content: space-between;
				align-items: center;

				.left {
					display: flex;
					flex-direction: row;
					align-items: center;

					.title {
						font-size: 40rpx;
						font-weight: bold;
						font-style: italic;
						color: #f9bd90;
					}

					.tips {
						font-size: 24rpx;
						color: #f9bd90;
						margin-left: 20rpx;
					}

				}

				.right {
					.button {
						padding: 8rpx 16rpx;
						color: 212849;
						border-radius: 30rpx;
						background: #f9bd90;
						font-size: 24rpx;
					}
				}
			}
		}
	}

	.main {
		padding: 0 24rpx;
	}

	.itemBox {
		background: #fff;
		padding: 0 24rpx;
		border-radius: 20rpx;
		overflow: hidden;
		margin-top: 24rpx;

		.titleBox {
			padding: 32rpx 0;
			border-bottom: 1rpx solid #eee;

			.title {
				font-size: 28rpx;
				font-weight: bold;
			}

			.word {
				font-size: 24rpx;
				color: #999;
			}
		}

		.grid-text {
			font-size: 24rpx;
			color: #333;
			padding-top: 10rpx;
		}
	}
</style>