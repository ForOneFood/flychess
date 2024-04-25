<template>
	<view class="my-body">
		<uv-navbar title=" " bg-color="rgba(250,250,255,.5)" @leftClick="goto('homepage')"></uv-navbar>
		<view class="title">
			<h1>真心话与大冒险</h1>
		</view>
		<view class="mid-square" :class="amt">
			<view class="show_result">
				<view class="type">{{ type }}</view>
				<view class="content">{{ content }}</view>
				<view class="buttons" v-show="isVisible">
					<uv-button class="btn" type="primary" @click="select('真心话')" text="真心话"></uv-button>
					<uv-button class="btn" type="primary" @click="select('大冒险')" text="大冒险"></uv-button>
				</view>
			</view>
		</view>
		<view class="bot-buttons">
			<uv-picker ref="picker" :columns="columns" @confirm="confirm"></uv-picker>
			<uv-button type="primary" icon="setting" @click="openPicker">选择模式</uv-button>
			<uv-button type="primary" class="btn" :disabled="disabled" icon="play-right-fill" iconColor="white"
				text="开始选择" @click="start"></uv-button>
		</view>
	</view>

	<uv-popup ref="popup">
		<view class="remark">
			<uv-text type="primary" size="18px" :text="pic_remark">
			</uv-text>
		</view>
	</uv-popup>

	<uv-toast ref="uvToast"></uv-toast>
</template>

<script>
	import selecttons from '@/common/truthordare/selections.json'
	export default {
		data() {
			return {
				columns: [
					[]
				],
				leval: null,
				type: null,
				content: null,
				amt: null,
				disabled: false,
				isVisible: false,
				zxh:null,
				dmx:null
			}
		},
		methods: {
			start() {
				this.disabled = false
				if (this.leval == null) {
					this.leval = selecttons.game_leval[0][0]
				}
				this.amt = 'poke_amt';
				this.type = null
				this.content = null
				this.disabled = true
				setTimeout(() => {
					this.type = "请选择"
					this.amt = 'poke_amt_over';
					this.isVisible = true
				}, 2000);
				
				// 获取符合的选项
				const filtered_items = selecttons.items.filter(item => item.leval == this.leval);

				// 随机选择内容
				const zxh_contents = filtered_items.filter(item => item.type == "真心话")[0].contents
				const dmx_contents = filtered_items.filter(item => item.type == "大冒险")[0].contents
				
				this.zxh = zxh_contents[Math.floor(Math.random() * zxh_contents.length)];
				this.dmx = dmx_contents[Math.floor(Math.random() * dmx_contents.length)];
				
				this.disabled = false
			},
			select(type) {
				
				if (type == "真心话") {
					this.content = this.zxh
				} else {
					this.content = this.dmx
				}
				
			},
			openPicker() {
				this.columns = selecttons.game_leval;
				this.$refs.picker.open();
			},
			confirm(e) {
				console.log(e.value[0])
				this.type = "选择了" + e.value[0]
				this.content = null
				this.leval = e.value[0]
				this.isVisible = false
			},
			goto(index) {
				uni.$uv.route({
					url: `/pages/${index}/${index}`,
				})
			},
		}
	}
</script>

<style lang="scss">
	@keyframes myfirst {
		0% {
			top: 0px;
			transform: rotate3d(0, 0, 0, 0deg);
		}

		40% {
			opacity: 0.3;
			top: 0px;
			transform: rotate3d(0, 1, 0, 90deg);
		}

		60% {
			opacity: 0.5;
			top: 0px;
			transform: rotate3d(0, 1, 0, 90deg);
		}

		100% {
			background: #fff395;
			top: 0px;
			transform: rotate3d(0, 1, 0, 0deg)
		}
	}

	.my-body {
		overflow: hidden;
		background-color: palegoldenrod;

		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;

		.title {
			margin-top: 60px;
		}

		.poke_amt {
			animation: myfirst 2s;
			animation-fill-mode: forwards;
		}

		.poke_amt_over {
			background: palegoldenrod !important;
		}

		.show_result {
			height: 80vh;
			display: flex;
			justify-content: space-between;
			margin-bottom: 2vh;
			flex-direction: column;
			align-items: center;

		}

		.mid-square {

			background-image: url('/static/img/puke.png');
			background-size: 100% 100%;
			background-repeat: no-repeat;
			background-position: center center;

			border-style: solid;
			border-color: 'red';
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: space-between;



			height: 65vh;
			width: 80vw;
			margin-top: 40px;

			.type {
				margin-top: 10px;
				font-size: 40px;
				color: #ff6913;
				text-shadow: 0 8px 10px #6699FF;
				font-weight: bolder;
				text-align: center;
				letter-spacing: 15px;
			}

			.content {
				margin-top: 40px;
				font-size: 30px;
				margin-left: 20px;
				margin-right: 20px;

			}

		}

		.buttons {
			display: flex;
			justify-content: flex-end;
			margin-bottom: 2vh;

			.btn {
				margin-right: 20px;
				padding-left: 20px;
			}
		}

		.bot-buttons {
			display: flex;
			justify-content: flex-end;
			margin-bottom: 2vh;
			padding: 20px;

			.btn {
				margin-right: 20px;
				padding-left: 20px;
			}
		}
	}
</style>