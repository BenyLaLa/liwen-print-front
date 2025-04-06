<template>
	<view class="container">
		<view class="item-text-1">订单编号:{{orderSn}}</view>
		<view class="item-text" v-if="status ==='已定稿'" @click="navigateToPage(item, isEditable)">
			您已完成定制！🎇

		</view>
		<view class="item-text" v-if="status === '待确认'&& hasNonEmptyTemplateId() "
			@click="navigateToPage(item, isEditable)">
			请最终确认您的定制！

		</view>
		<view class="item-text" v-if="status === '待确认'&& !hasNonEmptyTemplateId() "
			@click="navigateToPage(item, isEditable)">
			请开始您的定制！

		</view>

		<view class="list">

			<view v-for="(item, index) in customs" :key="index" class="list-item list-item-shadow"
				@mouseenter="hoverItem(index)" @mouseleave="leaveItem(index)">

				<!-- 包裹文字的容器 -->
				<view class="text-container">

					{{ item.templateName }}
				</view>
				<view class="button-container">
					<button @click="navigateToPage(item, isEditable)" v-if="status === '已定稿'" class="item-buttom">
						点击查看
					</button>
					<button @click="navigateToPage(item, isEditable)"
						v-if="status === '待确认' && item.templateId === null" class="item-buttom">
						选择模板
					</button>
					<button @click="navigateToPage(item, isEditable)" v-if="status === '待确认' && item.templateId != null"
						class="item-buttom">
						点击修改
					</button>
				</view>

			</view>
			<view class="item-text-add" v-if="status === '已定稿'">
				已定稿，不可修改，如需修改请退款后重新下单
			</view>
		</view>
		<button v-if="status === '待确认'&& hasNonEmptyTemplateId()" @click="showConfirmDialog()"
			class="save-button">确认定稿，定稿后将无法修改</button>
	</view>
</template>

<script>
	import config from '../../../config';
	import {
		eventBus
	} from '@/eventBus.js'; // 根据实际路径调整
	export default {
		data() {
			return {
				activeMenuIndex: null,
				customs: [],
				status: '',
				quantity: 1,
				showOptionsModal: false,
				showQuantityPicker: false,
				selectedImage: null,
				selectedQuantity: 1,
				hoverIndex: -1 // 新增：记录鼠标悬停的索引
			};
		},
		onLoad(options) {
			this.signature = options.signature;
			uni.setStorageSync('signature', {
				data: options.signature
			});


		},
		onShow() {
			this.handleShow();
		},
		methods: {
			handleShow() {
				uni.showLoading({
					title: '加载中...'
				});
				uni.request({
					url: config.BASE_URL + 'v1/order/getOrder', // 你的请求地址
					// url: "http://localhost:8089/api/v1/order/getOrder", // 你的请求地址

					method: 'GET', // 或 'POST'，根据你的需求
					data: {
						sign: this.signature,
					},
					success: (res) => {
						// 请求成功
						if (res.data.code === "0000") {
							// 假设状态码200表示请求成功
							console.log('请求成功', res.data);
							// 成功后跳转页面，并传递数据
							// 获取订单数据
							const orderData = res.data.data.order;
							const customsData = res.data.data.customs;
							this.customs = customsData;
							this.order = orderData;
							this.orderSn = orderData.sn;
							this.status = orderData.status;
							uni.setStorageSync('orderSn', {
								data: this.orderSn
							});
						} else {
							// 请求成功但状态码不是200
							console.log('请求失败', res.info);
							uni.showToast({
								title: '请确认网址是否有误',
								icon: 'none'
							});
						}
						uni.hideLoading();
					},
					fail: (err) => {
						// 请求失败
						console.log('请求失败', err);
						uni.showToast({
							title: '请求失败',
							icon: 'none'
						});
						uni.hideLoading();
					}
				});
			},

			clickButton(item) {
				uni.showModal({
					title: '温馨提示',
					content: '1、请控制字数，不能超过文本方框\n2、不支持表情包输入',
					success: (res) => {
						if (res.confirm) {
							this.navigateToPage(item);
						}
					}
				});
			},

			navigateToPage(item) {
				this.showOptionsModal = false;
				const showHint = this.status === '待确认'&& !this.hasNonEmptyTemplateId() ||this.status ==='已定稿';
				console.log(showHint);
				// 在跳转前存储数据
				if (item.templateId == null) {
					uni.reLaunch({
						url: '/pages/tabbar/templete-list/templete-list?id=' + item.id,
					});
				} else {
					uni.reLaunch({
						url: '/pages/tabbar/template/template?id=' + item.id + '&showHint=' + showHint,
					});
				}
			},
			showConfirmDialog() {
				uni.showModal({
					title: '确认定稿',
					content: '确认后将无法修改',
					success: (res) => {
						if (res.confirm) {
							uni.showLoading({
								title: '保存中...'
							})
							uni.request({
								url: config.BASE_URL + 'v1/order/status?orderSn=' + this
									.order.sn,
								// url: "http://localhost:8089/api/v1/order/status?orderSn=" + this
								// .order.sn, 
								success: (res) => {
									console.log(res);
									this.status = '已定稿';
									uni.hideLoading();
									uni.reLaunch({
										url: '/?signature=' +
											this.signature,
									});

								},
								fail: (err) => {
									uni.hideLoading();
									uni.showToast({
										icon: 'error',
										title: "确认定制失败,请刷新页面重试"
									})
								}
							})
						}
					}
				});
			},
			hoverItem(index) {
				this.hoverIndex = index;
			},
			leaveItem() {
				this.hoverIndex = -1;
			},
			hasNonEmptyTemplateId() {
				return this.customs.every(item => item.templateId !== null && item.templateId !== undefined && item
					.templateId !== '');
			}
		}
	}
</script>

<style scoped>
	.container {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		height: auto;
		background-color: #fff;
		touch-action: none;
		/* 禁止触摸拖动 */
		position: relative;
		/* 使内部绝对定位元素相对此容器定位 */
	}

	.save-button {
		width: 100%;
		background-color: #e95547;
		/* 绿色背景 */
		color: white;
		border: none;
		padding: 15px 20px;
		border-radius: 8px;
		font-size: 18px;
		cursor: pointer;
		transition: background-color 0.3s ease;
		/* 按钮颜色过渡效果 */
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
		/* 按钮阴影 */
	}

	.save-button:hover {
		background-color: #d04b3f;
		/* 鼠标悬停时的背景颜色 */
	}

	/* 导航栏按钮样式 */
	.nav-button {
		position: fixed;
		top: var(--status-bar-height);
		left: 330px;
		margin-top: 2px;
		height: 40px;
		z-index: 1000;
		background-color: #2196F3;
		/* 蓝色背景 */
		color: white;
		border: none;
		height: 40px;
		border-radius: 5px;
		font-size: 16px;
		cursor: pointer;
		transition: background-color 0.3s ease;
		/* 按钮颜色过渡效果 */
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
		/* 按钮阴影 */
	}

	.nav-button:hover {
		background-color: #1976D2;
		/* 鼠标悬停时的背景颜色 */
	}

	.afterConfirm {
		height: 70px;
		justify-content: center;
	}

	.list {
		flex: 1;
		padding: 20px;
		overflow-y: auto;
	}

	.list-item {
		display: flex;
		justify-content: space-between;
		/* 垂直居中 */
		/* align-items: center; */
		/* 水平居中 */
		padding: 20px;
		box-sizing: border-box;
		border-bottom: 1px solid #ddd;
		/* 浅灰色边框 */
		color: #333;
		/* 深色文字 */
		font-size: 18px;
		height: 120px;
		position: relative;
		transition: background-color 0.3s ease;
		/* 列表项背景颜色过渡效果 */
		cursor: pointer;
	}

	.item-text-2 {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 10px;
	}

	/* 文字容器 */
	.text-container {
		margin-top: 20px;
		font-size: 20px;
		height: 40px;
		/* 设置固定高度 */
		display: flex;
		align-items: center;
		/* 让文字垂直居中 */
	}

	/* 按钮容器 */
	.button-container {
		margin-top: 20px;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		height: 40px;
		/* 设置固定高度 */
	}

	/* 按钮样式 */
	.item-buttom {
		min-width: 80px;
	}

	.list-item:hover {
		background-color: #e0e0e0;
		/* 鼠标悬停时的背景颜色 */
	}

	.item-text {
		display: flex;
		flex-direction: column;
		/* 让子元素垂直排列 */
		justify-content: center;
		/* 垂直居中 */
		align-items: center;
		margin-top: 30px;
		text-align: center;
		position: relative;
		transition: background-color 0.3s ease;
		/* 列表项背景颜色过渡效果 */
		cursor: pointer;
		font-size: 20px;
		font-family: 'Arial', sans-serif;
		/* 更换字体 */
		border-bottom: 1px solid #ddd;
		/* 浅灰色边框 */
		color: #ff0000;
		padding: 20px;

	}

	.item-text-1 {
		display: flex;
		flex-direction: column;
		/* 让子元素垂直排列 */
		justify-content: center;
		/* 垂直居中 */
		align-items: center;
		margin-top: 30px;
		text-align: center;
		position: relative;
		transition: background-color 0.3s ease;
		/* 列表项背景颜色过渡效果 */
		cursor: pointer;
		font-size: 18px;
		font-family: 'Arial', sans-serif;
		/* 更换字体 */
		border-bottom: 1px solid #ddd;
		/* 浅灰色边框 */
		color: #333;
		padding: 20px;

	}

	.item-text-add {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		text-align: center;
		margin-top: 20px;
		color: #888;
		/* 灰色文字 */
		font-size: 16px;
	}

	.menu-button {
		background: none;
		border: none;
		color: #000;
		font-size: 18px;
		margin-left: 10px;
		cursor: pointer;
	}

	.menu {
		position: absolute;
		right: 0;
		background-color: #fff;
		border: 1px solid #ccc;
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
		z-index: 100;
		border-radius: 8px;
		margin-top: 10px;
	}

	.menu-item {
		padding: 10px 20px;
		color: #333;
		font-size: 16px;
		border-bottom: 1px solid #eee;
		cursor: pointer;
		transition: background-color 0.3s ease;
		/* 菜单项背景颜色过渡效果 */
	}

	.menu-item:hover {
		background-color: #f0f0f0;
		/* 鼠标悬停时的背景颜色 */
	}

	.menu-item:last-child {
		border-bottom: none;
	}

	.bottom-button {
		width: 100%;
		padding: 20px;
		background-color: #000;
		color: #fff;
		text-align: center;
		font-size: 20px;
		cursor: pointer;
		transition: background-color 0.3s ease;
		/* 按钮颜色过渡效果 */
	}

	.bottom-button:hover {
		background-color: #333;
		/* 鼠标悬停时的背景颜色 */
	}

	.options-modal {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, 0.5);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 100;
	}

	.options-list {
		background-color: #fff;
		border-radius: 5px;
		padding: 10px;
		width: 150px;
	}

	.option {
		padding: 10px;
		border-bottom: 1px solid #eee;
		cursor: pointer;
		transition: background-color 0.3s ease;
		/* 选项背景颜色过渡效果 */
	}

	.option:hover {
		background-color: #f0f0f0;
		/* 鼠标悬停时的背景颜色 */
	}

	.option:last-child {
		border-bottom: none;
	}

	.quantity-picker-modal {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, 0.5);
	}

	.view-button {
		background-color: #4CAF50;
		color: white;
		border: none;
		padding: 15px 20px;
		border-radius: 8px;
		font-size: 18px;
		cursor: pointer;
		transition: background-color 0.3s ease;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
	}

	.view-button:hover {
		background-color: #45a049;
	}

	.list-item-shadow {
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
		/* 设置阴影，水平偏移 0，垂直偏移 4px，模糊半径 8px，颜色为半透明黑色 */
		transition: box-shadow 0.3s ease;
		/* 添加阴影过渡效果 */
	}

	.list-item-shadow:hover {
		box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
		/* 鼠标悬停时，增加阴影的模糊半径和透明度 */
	}

	.item-buttom {
		background-color: #4CAF50;
		color: white;
		border: none;
		padding: 0px 8px;
		border-radius: 8px;
		font-size: 18px;
		cursor: pointer;
		transition: background-color 0.3s ease;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
	}
</style>