<template>
	<view>
		<uv-button type="primary" class="btn" icon="setting-fill" iconColor="white" text="设置"
			@click="set_menu"></uv-button>

	</view>

	<uv-popup ref="popup" class="popup">

		<view class="remark">
			<view class="select_item">
				<view class="title">选择类型</view>
				<view class="selects">
					<uv-radio-group v-model="default_book">
						<uv-radio style="font-size: 19px ; padding: 15px;" v-for="(item, index) in books" :key="index"
							:label="item" :name="item" @change="radioChange">
						</uv-radio>
					</uv-radio-group>
				</view>
			</view>
			<view class="select_item" v-for="(item, index) in book_conditions" :key="index">
				<view class="title">{{ item.selecttype }}</view>
				<view class="selects">
					<uv-checkbox-group>
						<uv-checkbox style="font-size: 19px ; padding: 10px;" checked
							v-for="(select, index) in item.selects" :key="index" :label="select"
							:name="item.selecttypeid" @change="checkboxChange($event,item.selecttypeid +'@' + select)">
						</uv-checkbox>
					</uv-checkbox-group>
				</view>
			</view>
		</view>
		<uv-button type="primary" shape="circle" text="确定" @click="summit"></uv-button>
	</uv-popup>
</template>

<script>
	// 将所有book导入
	import base from '@/common/selector/base.json'
	import food from '@/common/selector/food.json'
	import sport from '@/common/selector/sport.json'
	import sex from '@/common/selector/sex.json'
	import tasks from '@/common/selector/tasks.json'

	export default {
		data() {
			return {
				default_book: "任务卡片",
				books: [],
				select_book: "", // 那个json文件进行赛选
				book_conditions: [],
				return_data: [],

			}
		},
		emits: ['send'],
		mounted() {
			// 默认book
			this.select_book = 'tasks.json'
			// 生成所有的book，用来赛选
			this.books = base.filter(item => item.status === 0).map(item => item.name);
			// console.log('aaa',food)
			this.creat_conditions(tasks)

		},
		methods: {
			set_menu() {
				this.$refs.popup.open('center');
				// this.$emit("send", 'ABC')
			},
			summit() {
				this.$emit("send", {
					"book": this.select_book,
					"selects": this.return_data
				});
				this.return_data = []
				this.$refs.popup.close();
			},
			radioChange(n) {
				// 修改选择的book
				const filename = base.find(item => item.name === n).filename
				this.select_book = filename;
				// 获取book下的数据
				this.load_book(filename.replace('.json', '')).then(data => {
					this.creat_conditions(data)
				});

			},
			checkboxChange(index, item) {
				

				if (!index) {
					// 如果index为true，则向数组中添加元素A
					this.return_data.push(item);
				} else {
					// 如果index为false，则从数组中删除元素A
					const indexA = this.return_data.indexOf(item);
					if (indexA !== -1) {
						this.return_data.splice(indexA, 1);
					}
				}
				
			},
			async load_book(filename) {
				// 根据选择的书籍加载对应的 JSON 文件
				if (filename === 'food') {
					return food
				} else if (filename === 'sport') {
					return sport
				}else if (filename === 'sex') {
					return sex
				}else if (filename === 'tasks') {
					return tasks
				}
			},
			creat_conditions(data) {
				const selects = data.selects;
				const items = data.items;

				const result = [];
				for (let i = 0; i < selects.type.length; i++) {
					const selectType = selects.type[i];
					const selectTypeId = selects.typeid[i];
					const selectValues = new Set();
					items.forEach(item => {
						selectValues.add(item[selectTypeId]);
					});
					result.push({
						selecttype: selectType,
						selecttypeid: selectTypeId,
						selects: Array.from(selectValues)
					});
				}

				this.book_conditions = result;
			}

		}
	}
</script>

<style>
	.title {
		background-color: beige;
		font-size: 18px;
		width: 100vw;
		padding: 10px;
	}

	.remark {
		width: 80vw;
		height: 80vh;
		overflow-y: scroll;
	}

	.btn {
		margin-right: 10px;
	}
</style>