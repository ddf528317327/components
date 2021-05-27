<template>
	<view class="page bg-white" v-if="show">
		<view class="keyboard">
			<view class="keyboard-title text-df padding-lr-sm flex justify-between align-center">
				<text class="cuIcon-unfold no-show"></text>
				<text>中犇安全键盘</text>
				<text class="cuIcon-unfold" @click="close()"></text>
			</view>
			<view class="keyboard-content padding-lr-xs text-lg">
				<!-- 字母键盘时的数字键 start -->
				<view class="one flex justify-around align-center" :class="{'padding-32':!isLetter}" v-if="isLetter">
					<block >
						<view class="item" v-for="item in 10" :key="'oneNum'+item" @click="getValue(num[item-1])" :class="{'active':isActive == item+'num'}" @touchstart="touchstart($event,item+'num')" @touchend="touchend()">{{num[item-1]}}</view>
					</block>
				</view>
				<!-- 字母键盘时的数字键 end -->
				<!-- 键盘 start -->
				<view class="one flex justify-around align-center" :class="{'padding-32':!isLetter}" >
					<!-- 字母 -->
					<block v-if="isLetter">
						<view  class="item" v-for="item in 10" :key="'one'+item" @click="getValue(letter[item-1])" :class="{'active':isActive == letter[item-1]}" @touchstart="touchstart($event,letter[item-1])" @touchend="touchend()">{{letter[item-1]}}</view>
					</block>
					<!-- 数字 -->
					<block v-else>
						<view class="item item-num" v-for="item in 3" :key="'one'+item" @click="getValue(num[item-1])" :class="{'active':isActive == num[item-1]}" @touchstart="touchstart($event,num[item-1])" @touchend="touchend()">{{num[item-1]}}</view>
					</block>
				</view>
				<view class="two flex justify-around align-center" :class="{'padding-32':!isLetter}">
					<!-- 字母 -->
					<block v-if="isLetter">
						<view  class="item" v-for="item in 9" :key="'two'+item" @click="getValue(letter[item+9])" :class="{'active':isActive == letter[item+9]}" @touchstart="touchstart($event,letter[item+9])" @touchend="touchend()">{{letter[item+9]}}</view>
					</block>
					<!-- 数字 -->
					<block v-else>
						<view class="item item-num" v-for="item in 3" :key="'one'+item" @click="getValue(num[item+2])" :class="{'active':isActive == num[item+2]}" @touchstart="touchstart($event,num[item+2])" @touchend="touchend()">{{num[item+2]}}</view>
					</block>
				</view>
				<view class="three flex justify-around align-center" :class="{'padding-32':!isLetter}">
					<!-- 字母 -->
					<view v-if="isLetter" class="item case"  @click="switchCase(isUpperCase)" :class="{'active':isActive == 'switchCase'}" @touchstart="touchstart($event,'switchCase')" @touchend="touchend()">{{isUpperCase?'大写':'小写'}}</view>
					<block v-if="isLetter">
						<view  class="item" v-for="item in 7" :key="'three'+item" @click="getValue(letter[item+18])" :class="{'active':isActive == letter[item+18]}" @touchstart="touchstart($event,letter[item+18])" @touchend="touchend()">{{letter[item+18]}}</view>
					</block>
					<!-- 数字 -->
					<block v-else>
						<view class="item item-num" v-for="item in 3" :key="'one'+item" @click="getValue(num[item+5])" :class="{'active':isActive == num[item+5]}" @touchstart="touchstart($event,num[item+5])" @touchend="touchend()">{{num[item+5]}}</view>
					</block>
					<view v-if="isLetter" class="item delte flex justify-center align-center" @click="delte()" :class="{'active':isActive == 'delte'}" @touchstart="touchstart($event,'delte')" @touchend="touchend()" @longpress="allDelte()">
						<image class="img" src="./static/delete.png" mode="widthFix"></image>
					</view>
				</view>
				<view class="four flex justify-around align-center" :class="{'padding-32':!isLetter}">
					<!-- 字母数字切换 -->
					<view class="item num" :class="{'width-208':!isLetter,'active':isActive == 'switchNum'}" @click="switchNum(isLetter)"  @touchstart="touchstart($event,'switchNum')" @touchend="touchend()">{{isLetter?'123':'abc'}}</view>
					<!-- 空格或0 -->
					<view class="item space" :class="{'width-208':!isLetter,'active':isActive == '0' && !isLetter}" @click="!isLetter?getValue(0):''" @touchstart="touchstart($event,'0')" @touchend="touchend()">{{!isLetter?0:''}}</view>
					<!-- 确定或删除 -->
					<view class="item sure flex justify-center align-center" :class="{'width-208':!isLetter,'active':isActive == 'sure'}" @click="isLetter?close():delte()" @touchstart="touchstart($event,'sure')" @touchend="touchend()" @longpress="!isLetter?allDelte():''">
						<text v-if="isLetter" >确定</text>
						<image v-else class="img" src="./static/delete.png" mode="widthFix"></image>
					</view>
				</view>
				<!-- 键盘 end -->
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"fu-safe-keyboard",
		data() {
			return {
				letter:['q','w','e','r','t','y','u','i','o','p','a','s','d','f','g','h','j','k','l','z','x','c','v','b','n','m'],//英文
				num:[1,2,3,4,5,6,7,8,9,0],//数字
				specialSymbols:[],//字符
				isUpperCase:false,//英文大小写切换
				isLetter:true,//是否切换成小键盘
				show:false,//是否显示
				val:[],//输入字符暂存
				isActive:'',//动态效果
				type:'',//传入的字段名
			};
		},
		mounted() {
			
		},
		methods:{
			/**
			 * @description 按压
			 * @param {Object} e 事件对象
			 * @param {Object} val 传参
			 */
			touchstart(e,val){
				this.isActive= val;
				uni.vibrateShort({
				    success: function () {
				        // console.log('success');
				    }
				});
			},
			/**
			 * @description 抬起
			 */
			touchend(){
				this.isActive = '';
			},
			/**
			 * @description 大小写切换
			 * @param {Object} isUpperCase 是否是大写
			 */
			switchCase(isUpperCase){
				this.isUpperCase = !isUpperCase;
				let arr = [];
				this.letter.forEach(val=>{
					if(isUpperCase){
						arr.push(val.toLowerCase());
					}else{
						arr.push(val.toUpperCase());
					}
				})
				this.letter = arr;
			},
			/**
			 * @description 切换数字键盘
			 * @param {Object} isLetter 切换数字键盘
			 */
			switchNum(isLetter){
				this.isLetter = !isLetter;
			},
			/**
			 * @description 每次点击获取值
			 * @param {Object} val 每次点击获取的值
			 */
			getValue(val){
				// console.log(val)
				this.val.push(val);
				this.$emit('change',{type:this.type,val:this.val.join('')});
			},
			/**
			 * @description 关闭键盘
			 */
			close(){
				this.show = false;
				this.val = [];
			},
			/**
			 * @description 打开键盘
			 * @param {type,val} type 传入值的类型，建议和v-model样，这样多个input框赋值时方便， val传入input框已存在的值，便于继续输入
			 */
			open(type,val){
				this.show = true;
				this.type = type;
				if(val){
					this.val = val.split('');
				}
			},
			/**
			 * @description 删除字符
			 */
			delte(){
				if(this.val.length>0){
					let arr = this.val.pop();
					this.$emit('change',{type:this.type,val:this.val.join('')});
				}
				
			},
			/**
			 * @description 长按删除全部字符
			 */
			allDelte(){
				this.val = [];
				this.$emit('change',{type:this.type,val:''});
			}
		}
	}
</script>

<style lang="scss" scoped>
	.page{
		position: fixed;
		bottom: 0;
		padding-bottom:0;
		padding-bottom: constant(safe-area-inset-bottom);
		padding-bottom: env(safe-area-inset-bottom);
		width: 100%;
		z-index: 999999999999;
	}
	.keyboard{
		.keyboard-title{
			text-align: center;
			height: 72rpx;
			line-height: 72rpx;
			color: #828389;
		}
		.keyboard-content{
			.item{
				flex-shrink: 0;
				width: 64rpx;
				height: 72rpx;
				line-height: 72rpx;
				text-align: center;
				margin-bottom: 24rpx;
				background: #f2f1f7;
				border-radius: 4rpx;
				box-shadow: 0rpx 2rpx 2rpx 0rpx #d7d7db;
				font-weight: bold;
				&.active{
					background: #c5c5c5;
				}
				&.case,&.delte{
					width: 80rpx;
				}
				&.num,&.sure{
					width: 150rpx;
				}
				&.sure{
					.img{
						width: 42rpx;	
					}
				}
				&.space{
					width: 400rpx;
				}
				&.item-num{
					width: 200rpx;
					height: 104rpx;
					line-height: 104rpx;
				}
				
			}
			.two{
				padding: 0 32rpx;
				box-sizing: border-box;
			}
			.three{
				// padding: 0 100rpx;
				// box-sizing: border-box;
				position: relative;
				.delte{
					.img{
						width: 42rpx;
					}
				}
			}
		}
	}
	.width-208{
		width: 200rpx!important;
	}
	.padding-32{
		padding: 0 32rpx;
		box-sizing: border-box;
	}
	.no-show{
		opacity: 0;
	}
</style>
