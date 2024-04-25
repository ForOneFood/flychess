<template>
	<view class="my-body">
		<uv-navbar title="帮你选择"  bg-color="rgba(250,250,255,.5)" @leftClick="goto('homepage')"></uv-navbar>
		<view class="mid-square">
			<view class="show_result">
				<uv-image class="mid-image" mode="aspectFit" shape="circle" :src="pic_link"></uv-image>
				<view class="name" @click="show_detail">{{ pic_name }}</view>
				<view class="note">{{ pic_note }}</view>
			</view>
		</view>
		<view class="bot-buttons">
			<setmenu @send="get_set"></setmenu>
			<uv-button type="primary" class="btn" icon="play-right-fill" iconColor="white" text="开始选择"
				@click="start"></uv-button>
			<!-- <uv-button type="primary" class="btn" icon="play-right-fill" iconColor="white" text="测试页面"
				@click="goto('test')"></uv-button> -->
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
	import setmenu from '@/pages/selector/set_menu/set_menu.vue'
	import food from '@/common/selector/food.json'
	import sport from '@/common/selector/sport.json'
	import sex from '@/common/selector/sex.json'
	import tasks from '@/common/selector/tasks.json'
	export default {
		data() {
			return {
				pic_name: " ",
				pic_link: "/static/img/selector.png",
				pic_note: " ",
				pic_remark: "点击下方开始按钮",
				set_selects: {
					"book": "tasks",
					"selects": []
				}
			};
		},
		components: {
			setmenu
		},
		methods: {
			get_set(res) {
				this.set_selects = res
				this.load_book(res.book.replace('.json', '')).then(data => {
					// console.log('res', res)
					this.pic_name = "你选择了：" + data.name
				});
			},
			start() {
				const conditions = this.set_selects.selects;
				// console.log('aaa',food)
				this.load_book(this.set_selects.book.replace('.json', '')).then(data => {

					// console.log('conditions', this.set_selects.book.replace('.json', ''))
					let filtered_items = data.items;
					conditions.forEach(condition => {
						const key = condition.split("@")[0];
						const value = condition.split("@")[1];
						// console.log('condis', key, value, condition.key, filtered_items)
						filtered_items = filtered_items.filter(item => item[key] !== value);
					});
					// console.log('filtered_items', filtered_items)

					// 显示0.5s的加载中动画
					this.$refs.uvToast.show({
						type: 'loading',
						title: '正在加载',
						message: "正在加载",
						duration: 500
					})

					setTimeout(() => {
						if (filtered_items.length === 0) {
							// console.log(filtered_items)
							this.pic_name = "符合的选项没有了";
							this.pic_link = "";
							this.pic_note = " ";
							this.pic_remark = "没有内容了";
						} else {
							const res_item = this.choose_item(filtered_items);
							this.pic_name = res_item.name;
							this.pic_link = res_item.img;
							this.pic_note = res_item.note;
							this.pic_remark = res_item.remark;
						}

					}, 500);


				});

			},
			goto(index) {
				uni.$uv.route({
					url: `/pages/${index}/${index}`,
				})
			},
			show_detail() {
				// event.preventDefault();

				this.$refs.popup.open('center');

			},
			async load_book(filename) {
				// 根据选择的书籍加载对应的 JSON 文件
				// console.log("load_book1", filename)

				if(filename ==='food'){
					return food
				}else if (filename ==='sport'){
					return sport
				}else if (filename ==='sex'){
					return sex
				}else if (filename ==='tasks'){
					return tasks
				}

			},
			choose_item(items) {
				let randomIndex = Math.floor(Math.random() * items.length);
				let randomItem = items[randomIndex];
				return randomItem
			}
		}
	};
</script>

<style lang="scss">
	.remark {
		color: black;
		font-size: 19px;
		padding: 20px;
		width: 60vw;
	}

	.my-body {
		overflow: hidden;
		background-image: url('/static/img/bg-image.png');
		background-size: 100% 100%;
		background-repeat: no-repeat;
		background-position: center center;

		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;


		.mid-square {
			background-image: url('/static/img/mid-square.png');
			background-size: 100% 100%;
			background-repeat: no-repeat;
			background-position: center center;

			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: center;

			height: 65vh;
			width: 80vw;
			margin-top: 10vh;

			.show_result {
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;



				.mid-image {
					align-items: center;

				}

				.name {
					text-align: center;
					color: red;
					text-decoration: underline;
					cursor: pointer;
					font-size: 30px;
				}

				.note {
					color: black;
					font-size: 19px;
					padding: 20px;
				}
			}


		}


		.bot-buttons {
			display: flex;
			justify-content: flex-end;
			margin-bottom: 2vh;

			.btn {
				margin-right: 20px;
			}
		}
	}
</style>