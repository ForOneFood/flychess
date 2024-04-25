<template>
	<view>
		<view class="chess">
			<h1>飞行棋</h1>
			<view class="canvas-container">
				<canvas style="width: 360px; height: 600px;" canvas-id="firstCanvas" id="firstCanvas"></canvas>
				<canvas style="width: 360px; height: 600px; position: absolute; top: 0; left: 0;" canvas-id="player"
					id="player"></canvas>
				<!-- <canvas style="width: 360px; height: 600px; position: absolute; top: 0; left: 0;" canvas-id="boy"
					id="boy"></canvas> -->
			</view>
			<view class="mid_bnt">
				<view class="mid_con">
					<h1>{{ sex_now }}生摇色子</h1>
					<uv-image :src="img" width="80px" height="80px"></uv-image>
					<!-- <button @click="start" :disabled="disabled">点击摇色子</button> -->
					<view class="bot-buttons">
						<uv-picker ref="picker" :columns="columns" @confirm="confirm"></uv-picker>
						<uv-button type="primary" icon="setting" @click="openPicker">选择模式</uv-button>
						<uv-button type="primary" class="btn" :disabled="disabled" icon="play-right-fill"
							iconColor="white" text="开始选择" @click="start"></uv-button>
					</view>
				</view>
			</view>
		</view>
	</view>

	<uv-modal ref="modal" :content='content' @confirm="confirm_done" @close="confirm_done"></uv-modal>

</template>

<script>
	import chesssteps from '@/common/flyingchess/chesssteps.json'
	export default {
		data() {
			return {
				num_girl: 0,
				num_boy: 0,
				num_step: 0,
				sex_now: null,
				img: '../../static/flyingchess/chess/def.png',
				disabled: false,
				leval: null,
				step_info: null,
				content: null,
				columns: [
					[]
				],
			};
		},

		onReady: function(e) {
			const ctx = uni.createCanvasContext('firstCanvas')
			ctx.setLineWidth(3)
			// 画边框
			for (let i = 0; i < 11; i++) {
				for (let j = 0; j < 17; j++) {
					if ((i + j) % 2 === 0) {
						ctx.setStrokeStyle('blue');
					} else {
						ctx.setStrokeStyle('red');
					}
					if (i == 0 || j == 0 || i == 10 || j == 16) {
						ctx.strokeRect(i * 33, j * 33, 30, 30);
					}
				}
			}
			// 画背景
			ctx.draw()
			// 将图标放到第一格
			this.run('女')
			this.run('男')
			// 确定谁先走
			const randomNum = Math.random();
			if (randomNum < 0.5) {
				this.sex_now = '女';
			} else {
				this.sex_now = '男';
			}
		},
		methods: {
			openPicker() {
				this.columns = chesssteps.game_leval;
				this.$refs.picker.open();
			},
			confirm(e) {
				this.num_girl = 0
				this.num_boy = 0
				this.run('女')
				this.run('男')
				this.type = "选择了" + e.value[0]
				this.content = null
				this.leval = e.value[0]
				this.step_info = chesssteps.items.filter(item => item.leval == this.leval)[0];
			},
			confirm_done() {

				this.disabled = false
			},
			start() {
				this.disabled = true
				if (this.leval == null) {
					this.leval = chesssteps.game_leval[0][0]
					this.step_info = chesssteps.items.filter(item => item.leval == this.leval)[0];
				}
				// 生成随机数
				const items = [1, 2, 3, 4, 5, 6]
				this.num_step = items[Math.floor(Math.random() * items.length)];
				const timestamp = new Date().getTime();
				this.img = `../../static/flyingchess/chess/sifter${this.num_step}.gif?t=${timestamp}`

				setTimeout(this.show_note, 2000);
			},
			show_note() {
				if (this.sex_now == '女') {
					this.run_sts('女', this.num_step).then(() => {
						this.sex_now = '男';
						this.content = this.step_info.contents[this.num_girl]
						this.$refs.modal.open();
					})
				} else {
					this.run_sts('男', this.num_step).then(() => {
						this.sex_now = '女';
						this.content = this.step_info.contents[this.num_boy]
						this.$refs.modal.open();
					})
				}
			},
			run_sts(sex, num) {
				var i = 0
				return new Promise((resolve, reject) => {
					const moveBlock = () => {
						if (i < num) {
							this.run(sex);
							i++;
							setTimeout(moveBlock, 300); // 每隔一秒调用一次moveBlock函数
						} else {
							resolve();
						}
					}
					moveBlock();
				});


			},
			run(sex) {
				const ctx = uni.createCanvasContext('player')
				// 谁先走，后画谁
				if (sex == '女') {
					// 现将男生位置画上
					var position = this.number_to_position(this.num_boy)
					ctx.drawImage('../../static/flyingchess/boy.png', position.x, position.y, 20, 20)
					// 画女生位置
					this.num_girl = this.num_girl + 1
					if (this.num_girl > 52) {
						this.num_girl = 52
					}
					var position = this.number_to_position(this.num_girl)
					ctx.drawImage('../../static/flyingchess/girl.png', position.x, position.y, 20, 20)
				} else {
					var position = this.number_to_position(this.num_girl)
					ctx.drawImage('../../static/flyingchess/girl.png', position.x, position.y, 20, 20)
					this.num_boy = this.num_boy + 1
					if (this.num_boy > 52) {
						this.num_boy = 52
					}
					var position = this.number_to_position(this.num_boy)
					ctx.drawImage('../../static/flyingchess/boy.png', position.x, position.y, 20, 20)
				}
				ctx.draw()
			},
			number_to_position(num) {
				if (num < 12) {
					return {
						x: (11 - num) * 33 + 4,
						y: 0 + 4
					}
				} else if (num < 28) {
					return {
						x: 4,
						y: (num - 11) * 33 + 4
					}
				} else if (num < 38) {
					return {
						x: (num - 27) * 33 + 4,
						y: (27 - 11) * 33 + 4
					}
				} else if (num < 53) {
					return {
						x: (37 - 27) * 33 + 4,
						y: (27 - 11 - (num - 37)) * 33 + 4
					}
				} else {
					return {
						x: (37 - 27) * 33 + 4,
						y: (27 - 11 - (53 - 37) + 1) * 33 + 4
					}
				}
			}
		}
	}
</script>


<style lang="scss">
	.chess {
		overflow: hidden;
		background-color: peachpuff;
		background-size: 100% 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;

		.mid_bnt {
			position: absolute;

			.mid_con {
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;

				.bot-buttons {
					display: flex;
					justify-content: flex-end;
					margin-bottom: 2vh;

					.btn {
						margin-right: 20px;
						padding-left: 20px;
					}
				}
			}
		}

		.canvas-container {
			position: relative;
			width: 360px;
			height: 600px;
		}
	}
</style>