<template>
	<view>
		<swiper >
			<swiper-item v-for="data in info.slides" :key="data" @tap="handlePreview(data)">
				<image mode="widthFix" :src="'http://localhost:3000'+data"></image>
			</swiper-item>
		</swiper>
		
		<view>{{info.name}}</view>
		<view>{{info.price}}</view>
		<view>{{info.feature}}</view>
		<button @click="handleAdd">加入购物车</button>
		<button type="primary" @click="handleChange">跳转购物车</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				goodId:0,
				info:{}
			};
		},
		methods:{
			handlePreview(url){
				uni.previewImage({
					current:`http://localhost:3000`+url,
					urls:this.info.slides.map(item=>`http://localhost:3000`+item)
				})
			},
			
			handleAdd(){
				// console.log("dwa",uni.getStorageSync("token"))
				if(uni.getStorageSync("token")){
					//已经登录
					
					uni.request({
						url:`http://localhost:3000/carts?goodId=${this.goodId}`,
						success:(res)=>{
							// console.log(res.data.length)
							if(res.data.length===1){
								this.updateCart(res.data[0])
							}else{
								this.addCart()
							}
						}
					})
					
				}else{
					// 未登录
					uni.switchTab({
						url:"/pages/center/center"
					})
				}
			},
			
			addCart(){
				uni.request({
					url:'http://localhost:3000/carts',
					method:"POST",
					data:{
					  "goodId": this.goodId,
					  "checked": false,
					  "username": uni.getStorageSync("token").nickName,
					  "number": 1
					}
				})
			},
			updateCart(obj){
				uni.request({
					url:`http://localhost:3000/carts/${obj.id}`,
					method:"PUT",
					data:{
					 ...obj,
					 number:obj.number+1
					}
				})
			},
			handleChange(){
				uni.switchTab({
					url:"/pages/shopcar/shopcar",
					success: () => {
						
					}
				})
			}
		},
		onLoad(options) {
			console.log(options)
			this.goodId = options.id
		},
		mounted(){
			uni.request({
				url:`http://localhost:3000/goods/${this.goodId}`,
				success: (res) => {
					console.log(res.data)
					this.info  = res.data
				}
			})
		}
	}
</script>

<style lang="scss" scoped>
	swiper{
		height: 750rpx;
	}
	
	swiper-item{
		image{
			width: 100%;
		}
	}
</style>
