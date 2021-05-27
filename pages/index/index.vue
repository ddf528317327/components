<template>
	<view class="content">
		<input  type="text" v-model="value1" class="bg-white solids text-center" @focus="focus" />
		<input  type="text" v-model="value2" class="bg-white solids text-center" />
		<button @click="open('value1',value1)">弹起安全键盘1</button>
		<button @click="open('value2',value2)">弹起安全键盘2</button>
		<fu-safe-keyboard ref="keyboard" @change="change"></fu-safe-keyboard>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				value1: '',
				value2:''
			}
		},
		onLoad() {

		},
		methods: {
			open(type,val){
				this.$refs.keyboard.open(type,val);
			},
			change(data){
				this[data.type] = data.val;
			},
			focus(e){
				console.log(e)
			}
		}
	}
</script>

<style>
	input{
		width: 100%;
		height: 88rpx;
		
	}
</style>
