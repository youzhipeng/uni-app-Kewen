<template>
	<view>
		<!-- #ifdef MP -->
		<view>
			<button type="primary" open-type="getUserInfo" @getuserinfo="handleUserInfo" v-if="!user">获取授权</button>

			<view v-else class="user">
				<image :src="imgpath" @tap="handlePhoto"></image>
				<view>
					{{user.nickName}}
				</view>
				<button>
					完善信息
				</button>
			</view>
		</view>
		<!-- #endif -->

		<!-- h5的页面显示 -->
		<!-- #ifdef H5 -->
		<view>
			<!-- 大量form表单 -->
			<button @click="handleregister" v-if="!user">注册</button>
			<view v-else class="user">
				<image :src="imgpath" @tap="handlePhoto"></image>
				<view>
					{{user.nickName}}
				</view>
				
			</view>
		</view>
		<!-- #endif -->


		<!-- #ifdef H5 -->
		只有H5能看到
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN || MP-ALIPAY || MP-QQ -->
		WEIXIN，alipay,qq能看到
		<!-- #endif -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: null,
				imgpath: ""
			};
		},

		onLoad() {
			this.user = uni.getStorageSync("token")
			this.imgpath = this.user.avatarUrl
		},

		methods: {
			handleregister() {
				uni.request({
					url: "http://localhost:3000/users",
					method:"POST",
					data: {
						"nickName": "饮冰",
						"gender": 1,
						"language": "zh_CN",
						"city": "Dalian",
						"province": "Liaoning",
						"country": "China",
						"avatarUrl": "https://wx.qlogo.cn/mmopen/vi_32/DYAIOgq83epAvz1Q85rbZnRmKicDicF3uwySCicaY25pU2XKqItGzeQf3y1ibsr6raqnLnB2jBGrj7mzESGCHEO83Q/132"
					},
					success: (res) => {
						console.log(res.data)
						this.user = res.data
						this.imgpath = res.data.avatarUrl;
						uni.setStorageSync("token", this.user)
					}
				})
			},

			handleUserInfo(ev) {
				console.log(ev)
				//页面更新
				this.user = JSON.parse(ev.detail.rawData)
				//uni.storage
				this.imgpath = this.user.avatarUrl;
				
				uni.setStorageSync("token", this.user)
			},
			handlePhoto() {
				uni.chooseImage({
					count: 1,
					success: (res) => {
						// console.log(res.tempFilePaths[0])

						this.imgpath = res.tempFilePaths[0]

						uni.setStorageSync("token", {
							...uni.getStorageSync("token"),
							avatarUrl: this.imgpath
						})
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	/* #ifdef MP || H5 */
	.user {
		text-align: center;

		image {
			width: 200rpx;
			height: 200rpx;
			border-radius: 50%;
		}
	}

	/* #endif */
</style>
