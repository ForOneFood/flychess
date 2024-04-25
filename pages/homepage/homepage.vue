<template>
	<view class="body">
		<view class="title">
			<h1>选择吧</h1>
		</view>

		<view class="btn-group">
			<uv-button class="btn" type="primary" text="帮你选择" @click="goto('selector')"></uv-button>
			<uv-button class="btn" type="primary" text="真心话大冒险"  @click="goto('truthordare')"></uv-button>
			<uv-button class="btn" type="primary" text="飞行棋"  @click="goto('flyingchess')"></uv-button>
			<uv-button class="btn" type="primary" text="筛子" @click="goto('dices')"></uv-button>
			<uv-button class="btn" type="primary" text="测试" @click="goto('test')"></uv-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			}
		},
		methods: {
			goto(page) {
				uni.$uv.route({
					url: `/pages/${page}/${page}`,
				})
			},
		}
	}
</script>

<style>
	.body {
		background-color: bisque;
		height: 100vh;
		overflow: hidden;
	}

	.title {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 100px;
	}

	.btn-group {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 80px;
		margin-bottom: 10px;

	}

	.btn {
		margin-top: 20px;
		width: 300px;
	}
</style>