<template>
	<view>
		<checkbox-group @change="handldeAllChecked">
			<checkbox value="kerwin" :checked="isAllChecked"></checkbox>全选
		</checkbox-group>
		<view v-for="(data,index) in datalist" :key="data.id" class="box" @longpress="handlePress(data.id)">
			<checkbox @click="handleChecked(index)" :checked="data.checked"></checkbox>
			<image :src="'http://localhost:3000'+data.good.poster" mode="widthFix"></image>
			
			<view class="center">
				<view>
					{{data.good.name}}
				</view>
				<view>
					{{data.good.price}}
				</view>
			</view>
			
			<view class="right">
				<text @click="handleMinus(index)">-</text>
				<input :value="data.number"  disabled />
				<text @click="handleAdd(index)">+</text>
			</view>
		</view>
		
		<view >
			总金额-方法:{{ total()}}
		</view>
		<view>
			总金额-计算属性{{computedTotal}}
		</view>
		
		<common myname="aaaa"></common>
	</view>
</template>

<script>
	import common from '../../components/common.vue'
	export default {
		data() {
			return {
				datalist:[]
			};
		},
		components:{
			common
		},
		computed:{
			computedTotal(){
				// 依赖发生改变，计算属性会重新计算一遍
				var sum = 0;
				this.datalist.forEach(item=>{
					if(item.checked){
						sum+= item.number*item.good.price
					}
				})
				
				return sum
			},
			isAllChecked(){
				return this.datalist.every(item=>item.checked===true)
			}
		},
		onShow(){
			uni.request({
				url:"http://localhost:3000/carts?_expand=good",
				success: (res) => {
					console.log(res.data)
					this.datalist =res.data
				}
			})
		},
		methods:{
			total(){
				var sum = 0;
				this.datalist.forEach(item=>{
					if(item.checked){
						sum+= item.number*item.good.price
					}
				})
				
				return sum
			},
			
			handleChecked(index){
				console.log(index)
				this.datalist[index].checked = !this.datalist[index].checked
				
				//update更新接口
				this.update(this.datalist[index])
			},
			handleMinus(index){
				this.datalist[index].number--
				if(this.datalist[index].number===0){
					this.datalist[index].number = 1
				}
				this.update(this.datalist[index])
			},
			handleAdd(index){
				this.datalist[index].number++
				this.update(this.datalist[index])
			},
			update(obj){
				let {id,goodId,checked,username,number} = obj
				uni.request({
					url:`http://localhost:3000/carts/${obj.id}`,
					method:"PUT",
					data:{
						 goodId,
						 id,
						 checked,
						 username,
						 number
					}
				})
			},
			handlePress(id){
				uni.showModal({
					title:"提示",
					content:"你确定要删除?",
					success: (res) => {
						console.log(res)
						if(res.confirm){
							this.deleteCart(id)
						}
					}
				})
			},
			deleteCart(id){
				this.datalist = this.datalist.filter(item=>item.id!==id)
				uni.request({
					url:`http://localhost:3000/carts/${id}`,
					method:"DELETE",
					success: () => {
						
					}
				})
			},
			
			handldeAllChecked(ev){
				console.log(ev.detail.value)
				if(ev.detail.value.length===0){
					this.datalist = this.datalist.map(item=>({
						...item,
						checked:false
					}))
				}else{
					this.datalist = this.datalist.map(item=>({
						...item,
						checked:true
					}))
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
.box{
	display: flex;
	justify-content: space-around;
	image{
		width: 200rpx;
	}
	.center{
		width: 200rpx;
	}
	.right{
		display: flex;
		input{
			width: 100rpx;
			text-align: center;
		}
	}
}
</style>
