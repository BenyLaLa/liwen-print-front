<template>
    <view class="container">
        <u-navbar title="模板选择" left-icon="" bgColor="#F5F5F5">
        </u-navbar>
        <view class="image-row" v-for="(row, index) in imageRows" :key="index">
            <view class="image-item" v-for="(image, idx) in row" :key="idx">
                <!-- 如果是占位元素则不显示图片 -->
                <template v-if="image.src">
                    <image :src="image.src" class="thumbnail" mode="widthFix" @click="navigateToPage(image)" />
                    <view class="text-pic">{{image.templateId}}-{{image.templateName}}</view>
                </template>
            </view>
        </view>
    </view>
</template>

<script>
    import uniNavBar from '@dcloudio/uni-ui/lib/uni-nav-bar/uni-nav-bar.vue';

    export default {
        onLoad(options) {
            const _this = this;
            _this.id = options.id;
        },
        data() {
            return {
                images: [
                    {
                        src: '/static/img/template-1.JPG',
                        templateId: 1,
                        templateName: 'HelloKitty'
                    },
                    {
                        src: '/static/img/template-2.JPG',
                        templateId: 2,
                        templateName: '线条小狗生日'
                    },
                    {
                        src: '/static/img/template-3.JPG',
                        templateId: 3,
                        templateName: '线条小狗舞龙'
                    },
                    {
                        src: '/static/img/template-4.JPG',
                        templateId: 4,
                        templateName: '线条小狗2025'
                    },
                    {
                        src: '/static/img/template-5.JPG',
                        templateId: 5,
                        templateName: '线条小狗麻将'
                    },
                    {
                        src: '/static/img/template-6.JPG',
                        templateId: 6,
                        templateName: '线条小狗钞票'
                    },
                    {
                        src: '/static/img/template-7.JPG',
                        templateId: 7,
                        templateName: '线条小狗loveu'
                    },
                    {
                        src: '/static/img/template-8.JPG',
                        templateId: 8,
                        templateName: '小王子'
                    },
                    {
                        src: '/static/img/template-9.JPG',
                        templateId: 9,
                        templateName: '蜡笔小新全家福（新年）'
                    },
                    {
                        src: '/static/img/template-10.JPG',
                        templateId: 10,
                        templateName: '蜡笔小新合照'
                    },
                    {
                        src: '/static/img/template-11.JPG',
                        templateId: 11,
                        templateName: '蜡笔小新全家福'
                    },
                    {
                        src: '/static/img/template-12.JPG',
                        templateId: 12,
                        templateName: '蜡笔小新（生日1）'
                    },
                    {
                        src: '/static/img/template-13.JPG',
                        templateId: 13,
                        templateName: '蜡笔小新（生日2）'
                    },
                    {
                        src: '/static/img/template-14.JPG',
                        templateId: 14,
                        templateName: '猫和老鼠（生日）'
                    },
                    {
                        src: '/static/img/template-15.JPG',
                        templateId: 15,
                        templateName: '猫和老鼠花束'
                    },
                    {
                        src: '/static/img/template-16.JPG',
                        templateId: 16,
                        templateName: '猫和老鼠三格'
                    },
                    {
                        src: '/static/img/template-17.JPG',
                        templateId: 17,
                        templateName: '史迪仔-LOVEYOU'
                    },
                    {
                        src: '/static/img/template-18.JPG',
                        templateId: 18,
                        templateName: '史迪仔（生日）'
                    },
                    {
                        src: '/static/img/template-19.JPG',
                        templateId: 19,
                        templateName: '海绵宝宝合照（生日）'
                    },
                    {
                        src: '/static/img/template-20.JPG',
                        templateId: 20,
                        templateName: '海绵宝宝和派大星'
                    },
                    {
                        src: '/static/img/template-21.JPG',
                        templateId: 21,
                        templateName: '哆啦A梦四宫格'
                    },
                    {
                        src: '/static/img/template-22.JPG',
                        templateId: 22,
                        templateName: '三丽鸥'
                    },
                    {
                        src: '/static/img/template-23.JPG',
                        templateId: 23,
                        templateName: '库洛米'
                    },
                    {
                        src: '/static/img/template-24.JPG',
                        templateId: 24,
                        templateName: '麦当劳IP（生日）'
                    },
                    {
                        src: '/static/img/template-25.JPG',
                        templateId: 25,
                        templateName: '麦当劳IP合照'
                    }
                    // ...更多图片
                ]
            };
        },
        components: {
            uniNavBar
        },
        computed: {
            imageRows() {
                let rows = [];
                for (let i = 0; i < this.images.length; i += 2) {
                    let row = this.images.slice(i, i + 2);
                    // 如果当前行只有一个元素，添加一个占位元素
                    if (row.length === 1) {
                        row.push({ src: null });
                    }
                    rows.push(row);
                }
                return rows;
            }
        },
        methods: {
            navigateToPage(image) {
                const _this = this;
                console.log(image.templateName);
                if (_this.id!= null) {
                    uni.reLaunch({
                        url: '/pages/tabbar/template/template?templateId=' + image.templateId + '&templateName=' + image.templateName + '&id=' + _this.id,
                    });
                } else {
                    uni.reLaunch({
                        url: '/pages/tabbar/template-preview/template-preview?templateId=' + image.templateId
                    });
                }
            },
            clickImg(image) {
                uni.showModal({
                    title: '温馨提示',
                    content: '1、请控制字数，不能超过文本方框\n2、不支持表情包输入',
                    success: (res) => {
                        if (res.confirm) {
                            this.navigateToPage(image);
                        }
                    }
                });
            }
        }
    }
</script>

<style>
    .container {
        padding: 10px;
        padding-top: 50px;
    }

    .uni-nav-bar {
        z-index: 9999;
        /* 确保导航栏在最上层 */
        background-color: #ffffff;
        /* 设置背景色 */
        color: #000000;
        /* 设置文字颜色 */
    }

    .image-row {
        display: flex;
        flex-direction: row;
        margin-bottom: 10px;
    }

    .image-item {
        flex: 1;
        margin-right: 10px;
    }

    .image-item:last-child {
        margin-right: 0;
    }

    .thumbnail {
        width: 100%;
        cursor: pointer;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        /* 添加阴影效果 */
    }
   .text-pic {
        text-align: center; /* 水平居中 */
    }
</style>