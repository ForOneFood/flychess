<template>
	<view class="my-body">
		<view class="title">
			<h1>投骰子</h1>
		</view>
		<view class="dices">
			<view class="shaizi">
				<ul class="box box_amt_1" :style="{ animationPlayState:state }">
					<li v-for="(item, index) in dice1" :key="index">{{ item }}</li>

				</ul>
			</view>
			<view class="shaizi">
				<ul class="box box_amt_2" :style="{ animationPlayState:state }">
					<li v-for="(item, index) in dice2" :key="index">{{ item }}</li>
				</ul>
			</view>
		</view>
		<view class="bot-buttons">
			<uv-picker ref="picker" :columns="columns" @confirm="confirm"></uv-picker>
			<uv-button type="primary" icon="setting" @click="openPicker">选择模式</uv-button>
			<uv-button type="primary" class="btn" :disabled="disabled" icon="play-right-fill" iconColor="white"
				:text="btn_label" @click="play"></uv-button>
		</view>

	</view>
</template>

<script>
	import dices from '@/common/dices/dices.json'
	export default {
		data() {
			return {
				columns: [
					[]
				],
				dice1: [],
				dice2: [],
				amt: null,
				leval: null,
				state: 'paused',
				btn_label: '开始',
				disabled: false
			}
		},
		onLoad() {
			this.dice1 = dices.items[0].fun
			this.dice2 = dices.items[0].obj
		},
		methods: {
			openPicker() {
				this.columns = dices.game_leval;
				this.$refs.picker.open();
			},
			confirm(e) {
				this.type = "选择了" + e.value[0]
				this.content = null
				this.leval = e.value[0]
				const filtered_items = dices.items.filter(item => item.leval == this.leval);
				this.dice1 = filtered_items[0].fun
				this.dice2 = filtered_items[0].obj
			},
			play() {
				// 未选择则自动选择
				if (this.leval == null) {
					this.leval = dices.game_leval[0][0]
				}
				const filtered_items = dices.items.filter(item => item.leval == this.leval);
				this.dice1 = filtered_items[0].fun
				this.dice2 = filtered_items[0].obj

				this.btn_label = this.btn_label == '开始' ? '暂停' : '开始';
				this.state = this.state == 'running' ? 'paused' : 'running';

			}
		}
	}
</script>

<style lang="scss">
	.my-body {
		overflow: hidden;
		background-color: palegoldenrod;

		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-start;

		.title {
			margin-top: 30px;
		}

		.bot-buttons {
			display: flex;
			justify-content: flex-end;
			margin-top: -80px;

			.btn {

				padding-left: 20px;
			}
		}


	}

	.dices {

		transform: scale(0.5);
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
	}

	ul,
	li {
		margin: 0;
		padding: 0;
	}

	li {
		list-style: none;
	}

	.shaizi {
		width: 196px;
		margin: 150px auto;
		perspective: 1000px;
		padding-left: 60px;
	}

	.box {
		transform-style: preserve-3d;
		width: 196px;
		height: 196px;
		position: relative;
		/*transition: 20s;*/
		transform-origin: center center -98px;

	}

	.box_amt_1 {
		animation: turn1 2.1s infinite linear;
	}

	.box_amt_2 {
		animation: turn2 2.3s infinite linear;
	}


	.box li {
		width: 196px;
		height: 196px;
		font-size: 50px;
		text-align: center;
		line-height: 196px;
		color: black;
		position: absolute;
		top: 0;
		left: 0;
		background-image: url('/static/dice/bj.jpg');
	}

	.box li:nth-of-type(1) {
		background-position: -392px -392px;
		transform-origin: left;
		transform: rotateY(90deg);
	}

	.box li:nth-of-type(2) {
		background-position: 0px -196px;
		transform-origin: right;
		transform: rotateY(-90deg);
	}

	.box li:nth-of-type(3) {
		background-position: -196px -196px;
		transform-origin: top;
		transform: rotateX(-90deg);
	}

	.box li:nth-of-type(4) {
		background-position: -392px -196px;
		transform-origin: bottom;
		transform: rotateX(90deg);
	}

	.box li:nth-of-type(5) {
		background-position: -588px -196px;
		transform: translateZ(-196px);
	}

	.box li:nth-of-type(6) {
		background-position: -392px 0px;
		transform: translateZ(0px);
	}

	/*.shaizi:hover .box{
				transform: rotate3d(1,1,1,1800deg);
			}*/

	//  -1 180 0 180 5
	//  -1 180 0 90 4
	//  -1 180 0 270 3
	//  1 0 0 0 6
	//  -1 0 90 0 1
	//  -1 0 270 0 1
	// transform: scaleX(1) rotateZ(190deg) rotateY(190deg) rotateX(0deg);
	@keyframes turn1 {
		0% {
			transform: scaleX(-1) rotateZ(360deg) rotateY(90deg) rotateX(360deg);
		}

		20% {
			transform: scaleX(-1) rotateZ(540deg) rotateY(720deg) rotateX(270deg);
		}

		40% {
			transform: scaleX(-1) rotateZ(0deg) rotateY(270deg) rotateX(360deg);
		}

		60% {
			transform: scaleX(-1) rotateZ(180deg) rotateY(360deg) rotateX(90deg);
		}

		80% {
			transform: scaleX(-1) rotateZ(180deg) rotateY(720deg) rotateX(180deg);
		}

		100% {

			transform: scaleX(-1) rotateZ(360deg) rotateY(1080deg) rotateX(0deg);
		}

	}

	// 转速不同
	@keyframes turn2 {
		0% {
			transform: scaleX(-1) rotateZ(360deg) rotateY(90deg) rotateX(360deg);
		}

		20% {
			transform: scaleX(-1) rotateZ(540deg) rotateY(720deg) rotateX(270deg);
		}

		40% {
			transform: scaleX(-1) rotateZ(0deg) rotateY(270deg) rotateX(360deg);
		}

		60% {
			transform: scaleX(-1) rotateZ(180deg) rotateY(360deg) rotateX(90deg);
		}

		80% {
			transform: scaleX(-1) rotateZ(180deg) rotateY(720deg) rotateX(180deg);
		}

		100% {

			transform: scaleX(-1) rotateZ(360deg) rotateY(1080deg) rotateX(0deg);
		}

	}
</style>