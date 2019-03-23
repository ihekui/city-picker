<template>
	<view>
		<view class="city-picker-mask" v-if="show">
		<view class="city-picker" >
				<view class="city-picker-header">
					<text class="city-picker-control-left" @click="bindCancel">取消</text>
					<text class="city-picker-control-right" :style="{color:theme}" @click="bindSure">确定</text>
				</view>
				<picker-view class="city-pick-body" indicator-style="font-size:1.2em" indicator-class="indicator" @change="bindChange" :value="seclect">
					<picker-view-column>
						<view class="city-pick-body-item" v-for="(item,index) in province" :key="index">{{item.name}}</view>
					</picker-view-column>
					<picker-view-column>
						<view class="city-pick-body-item" v-for="(item,index) in city" :key="index">{{item.name}}</view>
					</picker-view-column>
					<picker-view-column v-show="mode==3">
						<view class="city-pick-body-item" v-for="(item,index) in district" :key="index">{{item.name}}</view>
					</picker-view-column>
				</picker-view>
				
			</view>
		</view>
	</view>
</template>

<script>
	import city from './data.js';
	export default {
		data() {
			return {
				indicator: "city-picker-indicator",
				visible: true,
				seclect: [2, 0, 0],
				province: [],
				city: [],
				district: [],
			}
		},
		props:{
			theme:{
				type:String,
				default:""
			},
			mode:{
				type:Number,
				default:3
			},
			show: {
				type:Boolean,
				default:false
			},
		},
		created() {
			this.initData()
		},
		methods: {
			bindCityPickShow() {
				let that = this;
				that.show = !that.show
				this.initData()
			},
			bindCancel(){
				this.$emit('bindCancel');
			},
			bindSure(){
				let that	= this;
				let result	= [
					that.province[that.seclect[0]],
					that.province[that.seclect[1]]
				]
				if (that.mode == 3) {
					result.push(that.province[that.seclect[2]])
				}
				// that.show	= !that.show;
				that.$emit('bindConfirm',result);
			},
			bindChange(e) {
				let that = this;
				let select_old = that.seclect;
				that.seclect = e.detail.value;
				if (e.detail.value[0] != select_old[0]) {
					//省变化，变动市区数据
					that.seclect[1] = that.seclect[2] = 0;
					that.initCity();
					that.initDistrict();
				} else {
					if (e.detail.value[1] !== select_old[1]) {
						//市变化，变动区县数据
						that.seclect[2] = 0;
						that.initDistrict()
					}
				}
			},
			objToAarray(obj) {
				let arr = []
				for (let i in obj) {
					arr.push({
						'code': i,
						'name': obj[i]
					})
				}
				return arr;
			},
			initData(current) {
				this.initProvince();
				this.initCity();
				this.initDistrict();
			},
			initProvince() {
				this.province = this.objToAarray(city[86])
			},
			initCity() {
				let that = this;
				that.city = [];
				that.city = that.objToAarray(city[that.province[that.seclect[0]].code]);
			},
			initDistrict() {
				let that = this;
				that.district = [];
				that.district = that.objToAarray(city[that.city[that.seclect[1]].code])
			}
		}
	}
</script>

<style>
	.city-picker-mask {
		position: fixed;
		z-index: 999;
		top: 0;
		right: 0;
		left: 0;
		bottom: 0;
		background: rgba(0, 0, 0, 0.6);
	}
	.city-picker {
		display: block;
		position: absolute;
		height: 42%;
		width: 100%;
		bottom: 0px;
		left: 0px;
		background: #FFFFFF;
	}

	.city-picker-header {
		display: block;
		height: 40px;
		line-height: 40px;
		padding: 0 20px;
		border-bottom: #EDEDED 1px solid;
		border-top: #EDEDED 1px solid;
	}

	.city-pick-body {
		height: 100%;
		text-align: center;
	}

	.city-picker-indicator {
		height: 30;
	}
	.city-pick-body-item {
		padding: 0 20px;
		line-height: 30px
	}

	.city-picker-control-left {
		float: left;
		color: #979797;
	}

	.city-picker-control-right {
		float: right;
	}
</style>
