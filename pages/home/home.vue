<template>
	<view> 
		<!--1-  dom结构 按照小程序的组件来写， 兼容没有问题 -->
		<!--2-  模板语法按照vue来写 -->
		<!--3-  生命周期小程序和vue的生命周期都支持 -->
		<!-- 4- wx替换成==>uni ,根据不同的平台自动转换成 相应对象 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000"
		 :circular="true"
		>
			<swiper-item v-for="data in looplist" :key="data.id">
				<image :src="'http://localhost:3000'+data.url" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		
		<view v-for="data in datalist" :key="data.id" class="box" @tap="handleChangePage(data.id)">
			<image :src="'http://localhost:3000'+data.poster" mode="widthFix"></image>
			<view>{{data.name}}</view>
			<view>
				{{data.price}}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				looplist:["aaaa","bbbb","cccc"],
				datalist:[],
				current:1, //当前页数
				total:0 //总数量
			};
		},
		methods:{
			handleChangePage(id){
				console.log(id)
				uni.navigateTo({
					url:`/pages/detail/detail?id=${id}&name=kerwin`
				})
			}	
		},
		mounted(){
			// console.log("mounted")
			uni.request({
				url:"http://localhost:3000/recommends",
				success: (res) => {
					console.log(res.data)
					this.looplist = res.data // 不是setData
				}
			})
			
			uni.request({
				url:"http://localhost:3000/goods?_page=1&_limit=5",
				success: (res) => {
					this.datalist = res.data
					// this.total = res.header["X-Total-Count"] ||  res.header["x-total-count"]
					// console.log(res.header)
					
					// 条件编译也可以在js
					// #ifdef H5
						this.total = res.header["x-total-count"]
					// #endif
					
					// #ifdef MP
						this.total = res.header["X-Total-Count"]
					// #endif
				}
			})
		
		},
		onReady() {
			// console.log("onready")
		},
		
		
		onPullDownRefresh() {
			console.log("下拉了")
			
			setTimeout(()=>{
				uni.stopPullDownRefresh()
			},2000)
		},
		
		onReachBottom() {
			if(this.datalist.length === parseInt(this.total)){
				return ;
			}
			console.log("到底了")
			this.current++;
			uni.request({
				url:`http://localhost:3000/goods?_page=${this.current}&_limit=5`,
				success: (res) => {
					this.datalist = [...this.datalist,...res.data]
					// this.total = res.header["X-Total-Count"]
				}
			})
		}
		
	}
</script>

<style lang="scss" scoped>
	swiper-item{
		image{
			width: 100%;
		}
	}
	swiper{
		height: 300rpx;
	}
	
	.box{
		overflow: hidden;
		padding: 10rpx;
		image{
			width: 200rpx;
			float: left;
		}
	}
</style>
