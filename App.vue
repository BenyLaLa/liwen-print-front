<script>
	export default {
		onLaunch: function() {
			console.log('App Launch');

			setTimeout(() => {
				uni.setTabBarBadge({
					index: 1,
					text: '31'
				});
				uni.showTabBarRedDot({
					index: 3
				});
			}, 1000);
		},
		onShow: function() {
			console.log('App Show');
		},
		onHide: function() {
			console.log('App Hide');
		}
	};
</script>

<style lang="scss">
	@import "@/uni_modules/uview-ui/index.scss";

	@font-face {
		font-family: "bull-word";
		src: url('./static/fonts/公牛号.ttf');
		font-weight: normal;
		font-style: normal;
	}

	@font-face {
		font-family: "hyqh45s";
		src: url('@/static/fonts/汉仪旗黑-45S.otf');
		font-weight: normal;
		font-style: normal;
	}

	@font-face {
		font-family: "hyqh40s";
		src: url('@/static/fonts/汉仪旗黑-40S.otf');
		font-weight: normal;
		font-style: normal;
	}

	@font-face {
		font-family: "msyh";
		src: url('./static/fonts/MSYH.ttf');
		font-weight: normal;
		font-style: normal;
	}

	@font-face {
		font-family: 'MyCustomFont';
		/* 自定义字体名称，后续使用该名称引用字体 */
		src: url('@/static/fonts/Z-R--阵全套4右20去斜杠.otf');
		/* 字体文件的路径 */
	}
	@font-face {
		font-family: 'NuFang';
		/* 自定义字体名称，后续使用该名称引用字体 */
		src: url('@/static/fonts/锐字锐线怒放黑简.TTF');
		/* 字体文件的路径 */
	}
	@font-face {
		font-family: 'FJALL';
		/* 自定义字体名称，后续使用该名称引用字体 */
		src: url('@/static/fonts/FJALLAONE-REGULAR.TTF');
		/* 字体文件的路径 */
	}
</style>