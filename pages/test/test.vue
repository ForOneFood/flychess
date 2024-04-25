<template>
	<view class="my-body">
		<view class="dices">
			<view class="shaizi">
				<uv-image :src="img"  width="80px"  height="80px"></uv-image>
			</view>

		</view>

		<uv-button @click="play" text="btn_label"></uv-button>


	</view>
</template>

<script>
	export default {
		data() {
			return {
				img:null,
				imageKey:1
			}
		},
		methods: {
			play() {
				console.log(this.imageKey)
				const timestamp = new Date().getTime();
				this.img = `../../static/flyingchess/chess/sifter1.gif?t=${timestamp}`;
	
			}
		}
	}
</script>

<style lang="scss">
	
	
</style>