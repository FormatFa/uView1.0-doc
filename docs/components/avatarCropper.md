## AvatarCropper 头像裁剪

该组件一般的图片裁剪需求场景，尤其适合于头像裁剪方面。

### 平台差异说明

|App|H5|微信小程序|支付宝小程序|百度小程序|头条小程序|QQ小程序|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|√|√|√|√|√|√|√|

### 基本使用

组件使用流程：
1. 打开头像裁剪页面，同时传递配置基本参数(已默认配置好最优参数)
2. 选取图片，调整图片合适位置和大小，确定裁剪并返回此裁剪结果
3. 在原始页面监听`uAvatarCropper`事件，获得裁剪结果

```html
<template>
	<view class="wrap">
		<view class="u-avatar-wrap">
			<image class="u-avatar-demo" :src="avatar" mode="aspectFill"></image>
		</view>
		<u-button @tap="chooseAvatar">进入裁剪页</u-button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				avatar: '/static/uview/common/logo.png',
			}
		},
		created() {
			// 监听从裁剪页发布的事件，获得裁剪结果
			uni.$on('uAvatarCropper', path => {
				this.avatar = path;
				// 可以在此上传到服务端
				uni.uploadFile({
					url: 'http://www.example.com/upload',
					filePath: path,
					name: 'file',
					complete: (res) => {
						console.log(res);
					}
				});
			})
		},
		methods: {
			chooseAvatar() {
				// 此为uView的跳转方法，详见"文档-JS"部分，也可以用uni的uni.navigateTo
				this.$u.route({
					// 关于此路径，请见下方"注意事项"
					url: '/uview/components/u-avatar-cropper/u-avatar-cropper',
					// 内部已设置以下默认参数值，可不传这些参数
					params: {
						// 输出图片宽度，高等于宽，单位px
						destWidth: 300,
						// 裁剪框宽度，高等于宽，单位px
						rectWidth: 200,
						// 输出的图片类型，如果'png'类型发现裁剪的图片太大，改成"jpg"即可
						fileType: 'jpg',
					}
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	.wrap {
		padding: 40rpx;
	}

	.u-avatar-wrap {
		margin-top: 80rpx;
		overflow: hidden;
		margin-bottom: 80rpx;
		text-align: center;
	}

	.u-avatar-demo {
		width: 150rpx;
		height: 150rpx;
		border-radius: 100rpx;
	}
</style>
```


### 注意事项

- 裁剪页面内置在uView中，由于打开页面，需要先在`pages.json`声明页面，故请把以下内容复制到项目根目录的`pages.json`中的`pages`数组中：

```js
{
	"path": "uview/components/u-avatar-cropper/u-avatar-cropper",
	"style": {
		"navigationBarTitleText": "头像裁剪",
		"navigationBarBackgroundColor": "#000000"
	}
}
```

- 裁剪后的结果，通过`uni.$on`监听`uAvatarCropper`事件，由于uniapp的限制，H5端裁剪的结果为`base64`格式，其他端为`blod`二进制形式，
如果后端对接收格式有要求，请自行处理


### API

以下参数，需要通过URL的get参数传参到裁剪页，非props。uView很多组件传递值的单位为`rpx`，注意这里的`dest-width`和`rect-width`单位为`px`

### URL参数

| 参数          | 说明            | 类型            | 默认值             |  可选值   |
|-------------  |---------------- |---------------|------------------ |-------- |
| dest-width | 输出图片宽度，高等于宽，单位**px**  | String \| Number | 200 | - |
| rect-width | 裁剪框宽度，高等于宽，单位**px**  | String \| Number | 200 | - |
| file-type | 输出的图片类型，如果'png'类型发现裁剪的图片太大，改成"jpg"即可  | String | jpg | png |


### Event

|事件名|说明|回调参数|版本|
|:-|:-|:-|:-|
| uAvatarCropper | 裁剪结束后的事件，通过`uni.$on`监听 | path: 裁剪的图片数据 | - |