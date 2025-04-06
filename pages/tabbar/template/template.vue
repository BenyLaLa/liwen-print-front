<template>

	<div :style="{ backgroundImage: 'url(' + imageUrl + ')' }" class="background-image">
		<u-navbar title="" left-icon="arrow-left" bgColor="#F5F5F5" @leftClick="leftClick" leftIconSize="25px">
			<!-- 新增下拉选择框 -->

		</u-navbar>

		<button v-if="!isDisable" class="nav-button-save" @click="showSaveConfirm()">保存</button>
		<button v-if="!isDisable" class="nav-button-fill" @click="handleClick()">文案参考</button>
		<button v-if="!isDisable" class="nav-button-select" @click="toPageTempleteList()">选择模板</button>
		<u-picker ref="addrPicker" :show="show" :columns="addrColumnsData" keyName="name" @change="changeAddress"
			@cancel="handleClose" @confirm="handleAddrConfirm">
		</u-picker>
		<div class="textareas-container">

			<input maxlength="5" v-model="value1" class="input-top-1"
				:style="{ marginTop: aStyle.marginTop, fontFamily: aStyle.fontFamily,fontSize: aStyle.fontSize }" :disabled="isDisable"></input>
			<input maxlength="53" v-model="value2" class="input-top-2" :disabled="isDisable"></input>
			<!-- <input maxlength="20" v-model="value3"  class="input-top-3"></input> -->

			<view class="input-container" :style="{ marginTop: cStyle.marginTop }">
				<!-- 使用 v-for 循环创建 6 个输入框 -->
				<input v-for="(index) in 6" :key="index" :class="['single-input', `single-input-${index}`]"
					:value="value3Arr[index - 1]" :maxlength="maxLengths[index - 1]"
					@input="handleInput(index - 1, $event)" @keydown.delete="handleDelete(index - 1)"
					:disabled="isDisable" />
			</view>
			<div class="mid-inputs-container-1">
				<input maxlength="1" v-model="value51" class="input-mid-2" :disabled="isDisable"></input>
				<input maxlength="38" v-model="value5" class="input-mid-1" :disabled="isDisable"></input>
				<input maxlength="4" v-model="value6" class="input-mid-num-1" :disabled="isDisable"></input>
			</div>
			<div class="mid-inputs-container-2">
				<input maxlength="1" v-model="value71" class="input-mid-2" :disabled="isDisable"></input>
				<input maxlength="38" v-model="value7" class="input-mid-1" :disabled="isDisable"></input>
				<input maxlength="4" v-model="value8" class="input-mid-num-1" :disabled="isDisable"></input>
			</div>
			<div class="mid-inputs-container-2">
				<input maxlength="1" v-model="value91" class="input-mid-2" :disabled="isDisable"></input>
				<input maxlength="38" v-model="value9" class="input-mid-1" :disabled="isDisable"></input>
				<input maxlength="4" v-model="value10" class="input-mid-num-1" :disabled="isDisable"></input>
			</div>
			<div class="mid-bottom-inputs-container-1">
				<input maxlength="8" v-model="value11" class="input-bottom-mid-1" :disabled="isDisable"></input>
				<input maxlength="1" v-model="value121" class="input-bottom-mid-2" :disabled="isDisable"></input>
				<input maxlength="41" v-model="value122" class="input-bottom-mid-3" :disabled="isDisable"></input>
			</div>
			<div class="mid-bottom-inputs-container-2">
				<input maxlength="8" v-model="value13" class="input-bottom-mid-1" :disabled="isDisable"></input>
				<input maxlength="1" v-model="value141" class="input-bottom-mid-2" :disabled="isDisable"></input>
				<input maxlength="41" v-model="value142" class="input-bottom-mid-3" :disabled="isDisable"></input>
			</div>
			<div class="mid-bottom-inputs-container-2">
				<input maxlength="8" v-model="value15" class="input-bottom-mid-1" :disabled="isDisable"></input>
				<input maxlength="1" v-model="value161" class="input-bottom-mid-2" :disabled="isDisable"></input>
				<input maxlength="41" v-model="value162" class="input-bottom-mid-3" :disabled="isDisable"></input>
			</div>
			<input maxlength="90" v-model="value17" class="input-bottom-1" :disabled="isDisable"></input>
			<input maxlength="90" v-model="value18" class="input-bottom-2" :disabled="isDisable"></input>
			<input maxlength="90" v-model="value19" class="input-bottom-2" :disabled="isDisable	"></input>
		</div>

	</div>

</template>

<script>
	import backtop from '../../../uni_modules/uview-ui/libs/config/props/backtop';
	import cityCode from '@/static/json/pca-code.json';
	import config from '../../../config';
	import {
		eventBus
	} from '../../../eventBus.js'; // 根据实际路径调整
	import {
		times
	} from '../../../uni_modules/uview-ui/libs/function/digit'

	export default {
		data() {
			return {
				imageUrl: '',
				// 手动初始化数组，为每个输入框设置不同的默认值
				value3Arr: ['2025', '02', '17', '05', '20', '00'],
				// 为每个输入框设置不同的最大输入长度
				maxLengths: [4, 2, 2, 2, 2, 2],

				"value1": "2025",
				"value2": "#麦先生的生日限定套餐",
				"value3": "2025/02/17 05:20:00",
				"value3Arr": [
					"2025",
					"02",
					"17",
					"05",
					"20",
					"00"
				],
				"value51": "#",
				"value71": "#",
				"value91": "#",
				"value5": "愿望成真套餐",
				"value6": "1",
				"value7": "有钱薯",
				"value8": "1",
				"value9": "对你的爱",
				"value10": "超级加倍",
				"value11": "配送",
				"value121": ":",
				"value122": "阿酱亲自送货上门",
				"value13": "",
				"value141": "",
				"value142": "(满意请给五星好评)",
				"value15": "收货人",
				"value161": ":",
				"value162": "亲爱的麦先生",
				"value17": "生日麦当当，今年响当当",
				"value18": "祝麦先生有“薯”不尽的财运",
				"value19": "Happy Birthday to you～",
				"aStyle": {
					"fontFamily": "bull-word",
					"fontSize": "75px",
					"marginTop": "560px"
				},
				"cStyle": {
					"marginTop": "44px"
				},
				styleName: 'A行数字',
				id: '',
				templateId: '',
				orderSn: '',
				isEditable: '',
				textareaCols: 40, // 假设textarea的列数是40
				isDisable: true,
				inputMidNumWidth: 72, // input-mid-num-1类输入框的宽度

				// 选择器
				addr: '',
				show: false,
				addrColumnsData: [], // 设置每一列的数据
			}
		},
		onLoad(options) {
			// debugger;
			const _this = this;
			const signature = uni.getStorageSync('signature');
			const orderSn = uni.getStorageSync('orderSn');
			const showHint = options.showHint;
			console.log(showHint);
			_this.templateId = options.templateId;
			console.log(_this.templateId);
			_this.templateName = options.templateName;
			console.log(_this.templateName);
			console.log(signature.data);
			_this.signature = signature.data;
			_this.id = options.id;
			console.log(orderSn);
			console.log(_this.id);
			_this.orderSn = orderSn.data;
			_this.searchCustom();
			_this.init();
			if (showHint === true) {
				_this.showHint();
			}
		

		},

		methods: {
			handleClick() {
				this.show = true
			},
			handleClose() {
				this.show = false
			},
			/** 省市区三级联动数据初始化*/
			async init() {
				// 赋初值
				this.addrColumnsData[0] = cityCode.map(item => {
					return {
						name: item.name,
						code: item.code,

					}
				})
				this.addrColumnsData[1] = cityCode[0].children.map(item => {
					return {
						name: item.name,
						code: item.code,

					}
				})
				this.addrColumnsData[2] = cityCode[0].children[0].children.map(item => {
					return {
						name: item.name,
						code: item.code,
						value: item.value
					}
				})
				// 微信小程序无法将picker实例传出来，只能通过ref操作
				const picker = this.$refs.addrPicker
				picker.setColumns(this.addrColumnsData)
				console.log("addrColumnsData", this.addrColumnsData)
			},
			/** 省市区三级联动选择地址改变*/
			changeAddress(e) {
				const {
					columnIndex, // 当前列下标
					value, // 为当前变化列的数组内容
					values, // 全部列数组内容
					index, // 当前值下标
					indexs, // 当前地区值下标
					picker = this.$refs.addrPicker, // 微信小程序无法将picker实例传出来，只能通过ref操作
				} = e

				// 第一列改变
				if (columnIndex === 0) {
					// 获取第二列的值
					let items = cityCode[index].children.map(item => {
						return {
							name: item.name,
							code: item.code,
						}
					})
					picker.setColumnValues(1, items)

					// 获得第二列初始化后的第三列的值
					items = cityCode[index].children[0].children.map(item => {
						return {
							name: item.name,
							code: item.code,
							value: item.value
						}
					})
					picker.setColumnValues(2, items)
				}

				// 第二列改变
				if (columnIndex === 1) {
					// 获得第三列的值
					let items = cityCode[indexs[0]].children[index].children.map(item => {
						return {
							name: item.name,
							code: item.code,
							value: item.value
						}
					})
					picker.setColumnValues(2, items)
				}
			},

			/** 省市区三级联动确认*/
			handleAddrConfirm(e) {
				const {
					indexs,
					values,
					value
				} = e
				this.valueCur = value[0].name + '-' + value[1].name + '-' + value[2].name + ':' + value[2].value;
				this.show = false
				console.log("styleName:", value[2].name)
				this.styleName = value[2].name;
				this.confirm(value[2].value);

				console.log("e", value[2].value.value1)
			},

			showSaveConfirm() {
				uni.showModal({
					title: '提示',
					content: '此步骤保存后仍可修改\n需最终确认定稿才算完成',
					success: (res) => {
						if (res.confirm) {
							this.saveData();
						}
					}
				});
			},
			showHint() {
				uni.showModal({
					title: '温馨提示',
					content: '1、请控制字数，不能超过文本方框\n2、不支持表情包输入',
					success: (res) => {

					}
				});
			},

			toPageTempleteList() {
				uni.reLaunch({
					url: "/pages/tabbar/templete-list/templete-list?id=" + this.id
				})
			},
			confirm(e) {
				const _this = this;
				console.log(e.value1);
				_this.show = false;
				_this.value1 = e.value1;
				_this.value2 = e.value2;
				_this.value3 = e.value3;
				_this.value3Arr = e.value3Arr;
				_this.value51 = e.value51;
				_this.value5 = e.value5;
				_this.value6 = e.value6;
				_this.value71 = e.value71;
				_this.value7 = e.value7;
				_this.value8 = e.value8;
				_this.value91 = e.value91;
				_this.value9 = e.value9;
				_this.value10 = e.value10;
				_this.value11 = e.value11;
				_this.value121 = e.value121;
				_this.value122 = e.value122;
				_this.value13 = e.value13;
				_this.value141 = e.value141;
				_this.value142 = e.value142;
				_this.value15 = e.value15;
				_this.value161 = e.value161;
				_this.value162 = e.value162;
				_this.value17 = e.value17;
				_this.value18 = e.value18;
				_this.value19 = e.value19;
				_this.aStyle = e.aStyle;
				_this.cStyle = e.cStyle;
			},
			showFillConfirm() {
				const _this = this;
				_this.show = true;
			},
			searchCustom() {
				const _this = this;

				uni.request({
					url: config.BASE_URL + 'v1/custom/getCustomById',
					method: 'GET', // 或 'POST'，根据你的需求
					data: {
						customId: _this.id,
						orderSn: _this.orderSn
					},
					success(res) {
						const contentObject = JSON.parse(res.data.data.content);

						console.log(contentObject);
						console.log(res.data.data);
						_this.value1 = contentObject.content1;
						_this.value2 = contentObject.content2;
						_this.value3 = contentObject.content3;
						_this.value51 = contentObject.content51;
						_this.value5 = contentObject.content5;
						_this.value6 = contentObject.content6;
						_this.value71 = contentObject.content71;
						_this.value7 = contentObject.content7;
						_this.value8 = contentObject.content8;
						_this.value91 = contentObject.content91;
						_this.value9 = contentObject.content9;
						_this.value10 = contentObject.content10;
						_this.value11 = contentObject.content11;
						_this.value121 = contentObject.content121;
						_this.value122 = contentObject.content122;
						_this.value13 = contentObject.content13;
						_this.value141 = contentObject.content141;
						_this.value142 = contentObject.content142;
						_this.value15 = contentObject.content15;
						_this.value161 = contentObject.content161;
						_this.value162 = contentObject.content162;
						_this.value17 = contentObject.content17;
						_this.value18 = contentObject.content18;
						_this.value19 = contentObject.content19;
						_this.isDisable = res.data.data.status;
						_this.orderSn = res.data.data.orderSn;

						if (res.data.data.astyle != null && res.data.data.astyle != '') {
							const aStyleObject = JSON.parse(res.data.data.astyle);
							const cStyleObject = JSON.parse(res.data.data.cstyle);
							_this.aStyle.fontSize = aStyleObject.fontSize;
							_this.aStyle.fontFamily = aStyleObject.fontFamily;
							_this.aStyle.marginTop = aStyleObject.marginTop;
							_this.cStyle.fontSize = cStyleObject.fontSize;
							_this.cStyle.fontFamily = cStyleObject.fontFamily;
							_this.cStyle.marginTop = cStyleObject.marginTop;
						}

						// 按分隔符分割字符串
						_this.value3Arr = contentObject.content3.split(/[/ :]/);

						_this.id = res.data.data.id;

						if (_this.templateId == null) {
							_this.templateId = res.data.data.templateId;
						}

						if (_this.templateName == null) {
							_this.templateName = res.data.data.templateName;
						}

						_this.styleName = res.data.data.styleName;

						_this.imageUrl = 'static/img/template-set-' + _this.templateId + '.jpg';

						console.log("search");
						uni.hideLoading();
					},
					fail: (err) => {
						uni.hideLoading();
					}
				})
			},
			leftClick() {
				uni.reLaunch({
					url: '/pages/tabbar/custom-list-new/custom-list-new?signature=' + this.signature // 目标页面路径
				});
			},
			handleInputAch(event) {
				// 使用正则表达式过滤非中文字符
				const value = event.mp.detail.value.replace(/[^\u4e00-\u9fa5]/g, '');
				this.value1 = value;
				console.log(event.mp.detail.value);
			},
			handleInput(index, event) {
				const _this = this;
				const separators = ['/', '/', ' ', ':', ':', ''];
				const value = event.mp.detail.value;
				_this.value3Arr[index] = value;
				// 如果输入有值，自动聚焦到下一个输入框
				if (value && index < 5) {
					_this.$nextTick(() => {
						if (uni.getSystemInfoSync().platform === 'mp-weixin' || uni.getSystemInfoSync()
							.platform === 'mp-alipay') {
							// 仅在小程序平台使用 node 方法
							uni.createSelectorQuery().select(`.single-input:nth-child(${index + 2})`).node().exec((
								res) => {
								const input = res[0].node;
								if (input) {
									input.focus();
								}
							});
						} else {
							// 在其他平台使用其他方式聚焦输入框
							uni.createSelectorQuery().select(`.single-input:nth-child(${index + 2})`)
								.boundingClientRect((rect) => {
									if (rect) {
										const input = document.querySelector(
											`.single-input:nth-child(${index + 2})`);
										if (input) {
											input.focus();
										}
									}
								}).exec();
						}
					});
				}
				let result = '';
				for (let i = 0; i < _this.value3Arr.length; i++) {
					result += _this.value3Arr[i] + separators[i];
				}
				_this.value3 = result;
				console.log(_this.value3);
			},
			// 处理删除事件
			handleDelete(index) {
				this.value3Arr[index] = '';
				// 如果当前输入框为空，自动聚焦到上一个输入框
				if (index > 0) {
					this.$nextTick(() => {
						const platform = uni.getSystemInfoSync().platform;
						if (platform === 'mp-weixin' || platform === 'mp-alipay') {
							// 仅在小程序平台使用 node 方法
							uni.createSelectorQuery().select(`.single-input:nth-child(${index})`).node().exec((
								res) => {
								const input = res[0].node;
								if (input) {
									input.focus();
								}
							});
						} else {
							// 在其他平台（如 H5）使用传统方式聚焦输入框
							uni.createSelectorQuery().select(`.single-input:nth-child(${index})`)
								.boundingClientRect((rect) => {
									if (rect) {
										const input = document.querySelector(
											`.single-input:nth-child(${index})`);
										if (input) {
											input.focus();
										}
									}
								}).exec();
						}
					});
				}
			},
			// 判断输入框是否禁用
			// isDisabled(index) {
			// 	if (index === 0) return false;
			// 	return this.value3Arr[index - 1] === '';
			// },
			saveData() {
				const _this = this;
				// 这里应该包含保存数据的逻辑，例如发起网络请求
				// console.log(_this.id);
				const id = _this.id;
				const params = {
					id: id,
					content: {
						content1: _this.value1,
						content2: _this.value2,
						content3: _this.value3,
						content51: _this.value51,
						content5: _this.value5,
						content6: _this.value6,
						content71: _this.value71,
						content7: _this.value7,
						content8: _this.value8,
						content91: _this.value91,
						content9: _this.value9,
						content10: _this.value10,
						content11: _this.value11,
						content121: _this.value121,
						content122: _this.value122,
						content13: _this.value13,
						content141: _this.value141,
						content142: _this.value142,
						content15: _this.value15,
						content161: _this.value161,
						content162: _this.value162,
						content17: _this.value17,
						content18: _this.value18,
						content19: _this.value19,

					},
					aStyle: {
						fontSize: _this.aStyle.fontSize,
						fontFamily: _this.aStyle.fontFamily,
						marginTop: _this.aStyle.marginTop
					},
					cStyle: {
						fontSize: _this.cStyle.fontSize,
						fontFamily: _this.cStyle.fontFamily,
						marginTop: _this.cStyle.marginTop
					},
					styleName: _this.styleName,
					orderSn: _this.orderSn,
					picUrl: "",
					templateId: _this.templateId,
					templateName: _this.templateName,
					quantity: "1"
				};
				console.log("保存：", params);
				uni.showLoading({
					title: '保存中...'
				});
				uni.request({
					url: config.BASE_URL + 'v1/custom/saveCustom',
					// url:'http://localhost:8089/api/v1/custom/saveCustom',
					method: 'POST',
					data: params,

					success: (res) => {
						uni.reLaunch({
							url: '/pages/tabbar/custom-list-new/custom-list-new?signature=' + this
								.signature // 目标页面路径
						});
						uni.hideLoading();
					}
				})

			}
		},
	}
</script>


<style>
	input {
		border: 1px solid #ffc9ca;
		border-radius: 4px;
		/* padding: 8px; */
	}

	.background-image {
		width: 400px;
		/* 背景图宽度 */
		height: 1400px;
		/* 背景图高度，根据需要调整 */
		background-size: cover;
		/* 背景图覆盖整个容器 */
		background-position: center;
		/* 背景图居中显示 */
		display: flex;
		/* 使用Flexbox布局 */
		justify-content: center;
	}

	/* 导航栏按钮样式 */
	.nav-button-save {
		position: fixed;
		top: var(--status-bar-height);
		/* 兼容刘海屏 */
		left: 310px;
		margin-top: 2px;
		height: 40px;
		z-index: 1000;
		/* 确保按钮在内容上方 */
		background-color: #4CAF50;
		/* 黑色背景 */
		color: white;
		/* 白色文字 */
		border: none;
		/* 无边框 */
		height: 40px;
		border-radius: 5px;
		/* 圆角 */
		font-size: 16px;
		/* 字体大小 */
		/* 其他你可能需要的样式，例如字体样式、边框阴影等 */
	}

	.nav-button-fill {
		position: fixed;
		top: var(--status-bar-height);
		/* 兼容刘海屏 */
		left: 210px;
		margin-top: 2px;
		height: 40px;
		z-index: 1000;
		/* 确保按钮在内容上方 */
		background-color: #4CAF50;
		/* 黑色背景 */
		color: white;
		/* 白色文字 */
		border: none;
		/* 无边框 */
		height: 40px;
		border-radius: 5px;
		/* 圆角 */
		font-size: 16px;
		/* 字体大小 */
		/* 其他你可能需要的样式，例如字体样式、边框阴影等 */
	}

	.nav-button-select {
		position: fixed;
		top: var(--status-bar-height);
		/* 兼容刘海屏 */
		left: 110px;
		margin-top: 2px;
		height: 40px;
		z-index: 1000;
		/* 确保按钮在内容上方 */
		background-color: #4CAF50;
		/* 黑色背景 */
		color: white;
		/* 白色文字 */
		border: none;
		/* 无边框 */
		height: 40px;
		border-radius: 5px;
		/* 圆角 */
		font-size: 16px;
		/* 字体大小 */
		/* 其他你可能需要的样式，例如字体样式、边框阴影等 */
	}

	.textareas-container {
		resize: none;
		/* 禁止拖动 */
		display: flex;
		/* 文本框容器使用Flexbox布局 */
		flex-direction: column;
		/* 文本框垂直排列 */

		width: 100%;
		/* 容器宽度 */
		overflow: hidden;
	}



	.input-top-1 {
		position: absolute;
		resize: none;
		/* 禁止拖动 */
		font-family: bull-word, sans-serif;
		font-size: 20px;
		text-align: center;
		margin-top: 480px;
		margin-left: 76px;
		width: 240px;
		/* height: 70px; */
		position: relative;
		overflow: hidden;
		border: 1px solid #ececec;
	}

	.input-top-2 {
		font-family: 'hyqh45s', sans-serif;
		font-size: 19px;
		font-weight: 700;
		margin-top: 646px;
		margin-left: 62px;
		text-align: center;
		width: 268px;
		height: 30px;
		overflow: hidden;
		position: absolute;
	}

	.input-top-3 {
		font-family: 'FJALL';
		font-size: 20px;
		margin-top: 48px;
		margin-left: 150px;
		font-weight: 700;
		width: 40px;
		height: 30px;
		overflow: hidden;
		position: relative;
	}

	.input-top-4 {
		font-family: hyqh45s, sans-serif;
		font-size: 16px;
		margin-top: 9px;
		margin-left: 160px;
		font-weight: 700;
		overflow: hidden;
		width: 200px;
		height: 30px;
		position: relative;
	}

	.mid-inputs-container-1 {
		display: flex;
		margin-top: 34px;
		margin-left: 60px;
		font-weight: 700;
		overflow: hidden
	}

	.input-mid-1 {
		font-family: msyh, sans-serif;
		/* box-sizing: border-box; */
		font-size: 17px;
		margin-left: 2px;
		width: 180px;
		height: 24px;
		white-space: nowrap;
		/* 防止文本自动换行 */
		overflow: hidden;
		/* border: 1px solid black; */
		overflow-x: auto;
		scroll-left: 0;
		/* background: linear-gradient(to right, white 100%, transparent 0%); */
		border: 1px solid #ececec;
	}

	.input-mid-2 {
		font-family: msyh, sans-serif;
		/* box-sizing: border-box; */
		font-size: 17px;

		width: 10px;
		height: 24px;
		white-space: nowrap;
		/* 防止文本自动换行 */
		overflow: hidden;
		/* border: 1px solid black; */
		overflow-x: auto;
		scroll-left: 0;
		border: 1px solid #ececec;
		/* background: linear-gradient(to right, white 100%, transparent 0%); */
	}

	.input-mid-num-1 {
		position: absolute;
		font-family: 'msyh', sans-serif;
		font-size: 17px;
		text-align: center;
		margin-left: 200px;
		margin-top: 0px;
		width: 72px;
		height: 24px;
		border: 1px solid #ececec;
	}

	.mid-bottom-inputs-container-1 {
		display: flex;
		margin-top: 14px;
		margin-left: 55px;
		font-weight: 700;
		overflow: hidden
	}

	.input-bottom-mid-1 {
		font-family: msyh, sans-serif;
		/* box-sizing: border-box; */
		font-size: 17px;
		margin-left: 20px;
		width: 54px;
		height: 20px;
		border: 1px solid #ececec;
		scroll-left: 0;
		/* background: linear-gradient(to right, white 100%, transparent 0%); */

	}

	.input-bottom-mid-2 {
		position: absolute;
		font-family: msyh, sans-serif;
		font-size: 17px;
		text-align: left;
		margin-left: 74px;
		width: 8px;
		height: 20px;
		border: 1px solid #ececec;


	}

	.input-bottom-mid-3 {
		position: absolute;
		font-family: msyh, sans-serif;
		font-size: 17px;
		text-align: left;
		margin-left: 84px;
		width: 193px;
		height: 20px;


	}

	.mid-inputs-container-2 {
		display: flex;

		margin-top: 10px;
		margin-left: 60px;
		font-weight: 700;
	}

	.mid-bottom-inputs-container-2 {
		display: flex;

		margin-top: 6px;
		margin-left: 55px;
		font-weight: 700;
		width: 300px;

	}

	/* .mid-inputs-container-3 {
	    display: flex;
	    justify-content: flex-start;
	    
	    margin-top: 20px;
	    margin-left: 9px;
	} */



	.input-mid-3 {
		font-family: msyh, sans-serif;
		font-size: 20px;
		margin-left: 160px;
		margin-top: 9px;
		width: 200px;
		height: 28px;
		text-align: left;
		font-weight: 700;
		/* border: 1px solid black; */
	}

	.input-bottom-1 {
		font-family: msyh, sans-serif;
		font-size: 17px;
		margin-top: 35px;
		margin-left: 74px;
		text-align: left;
		font-weight: 700;
		height: 24px;
		width: 258px;
		/* border: 1px solid black; */
		/* line-height: 1.5; */

	}

	.input-bottom-2 {
		font-family: msyh, sans-serif;
		font-size: 17px;
		margin-top: 3px;
		margin-left: 74px;
		text-align: left;
		font-weight: 700;
		height: 24px;
		width: 258px;
		/* border: 1px solid black; */
		/* line-height: 1.5; */

	}

	.input-container {
		display: flex;
		flex-direction: row;
		margin-left: 151px;
		margin-top: 30px;
		height: 30px;
		width: 220px;
		font-size: 20px;

	}

	.single-input {
		font-family: FJALL, sans-serif;
		font-weight: 700;
		/* height: 20px; */
		/* margin: 0 5px; */
		text-align: center;
		/* font-size: 17px; */
	}

	/* 为每个输入框指定不同的宽度 */
	.single-input-1 {

		width: 40px;
	}

	.single-input-2 {
		margin-left: 2px;
		width: 20px;
	}

	.single-input-3 {
		margin-left: 2px;
		width: 20px;
	}

	.single-input-4 {
		margin-left: 4px;
		width: 20px;
	}

	.single-input-5 {
		margin-left: 3px;
		width: 20px;
	}

	.single-input-6 {
		margin-left: 3px;
		width: 20px;
	}
</style>