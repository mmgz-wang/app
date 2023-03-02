<template>
	<view class="content">
		<image class="logo" src="/static/logo.png" />
		<view class="btn">
			<button>获取数据</button>
			<button>上传图片</button>
		</view>
<!-- 		<uni-table ref="table" :loading="loading" border stripe type="selection" emptyText="暂无更多数据"
			@selection-change="selectionChange">
			<uni-tr>
				<uni-th width="150" align="center">id</uni-th>
				<uni-th width="150" align="center">姓名</uni-th>
				<uni-th width="150" align="center">备注</uni-th>
				<uni-th width="150" align="center">手机号</uni-th>
				<uni-th align="center">地址</uni-th>
				<uni-th width="204" align="center">设置</uni-th>
			</uni-tr>
			<uni-tr v-for="(item, index) in state.tableData" :key="index">
				<uni-td>{{ item.id }}</uni-td>
				<uni-td>
					<view class="name">{{ item.displayName }}</view>
				</uni-td>
				<uni-td align="center">{{ item.phoneNumbers[0].value }}</uni-td>
				<uni-td align="center">{{ item.address }}</uni-td>
				<uni-td>
					<view class="uni-group">
						<button class="uni-button" size="mini" type="primary">修改</button>
						<button class="uni-button" size="mini" type="warn">删除</button>
					</view>
				</uni-td>
			</uni-tr>
		</uni-table> -->
	</view>
</template>

<script setup lang="ts">
	import {
		ref,
		reactive
	} from 'vue'
	const title = ref('Hello')
	const loading = ref(true)
	const state = reactive({
		tableData: [],
		name:'123243'
	})
	const selectionChange = () => {

	}
	uni.getLocation({
		altitude:true,
		highAccuracyExpireTime:6000,
		isHighAccuracy:true,
		success:(res) => {
			uni.showModal({
				content: `当前位置的经度：${res.longitude},当前位置的纬度：${res.latitude}`,
				showCancel: false
			});
		}
	})
	// 打开地图
	uni.getLocation({
		type: 'gcj02', //返回可以用于uni.openLocation的经纬度
		success: function(res) {
			const latitude = res.latitude;
			const longitude = res.longitude;
			uni.openLocation({
				latitude: latitude,
				longitude: longitude,
				success: function() {
					uni.showModal({
						content: 'success',
						showCancel: false
					});
				}
			});
		}
	});

	// uni.chooseImage({
	// 	count: 6, //默认9
	// 	sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
	// 	sourceType: ['album'], //从相册选择
	// 	success: function(res) {
	// 		uni.showModal({
	// 			content: `${JSON.stringify(res.tempFilePaths)}`,
	// 			showCancel: false
	// 		});
	// 	}
	// });

	// let res = uni.getAppAuthorizeSetting()
	// //获取设备信息
	// uni.showModal({
	// 	content: `${JSON.stringify(res)}`,
	// 	showCancel: false
	// });

	// const msg = plus.messaging.createMessage(plus.messaging.TYPE_SMS)
	// msg.to = ['15321898686']
	// msg.body = '111'
	// msg.silent = true
	// plus.messaging.sendMessage(msg,function(){
	// 	uni.showModal({
	// 		content: 'success',
	// 		showCancel: false
	// 	});
	// },function(){
	// 	uni.showModal({
	// 		content: 'error',
	// 		showCancel: false
	// 	});
	// })

	// const msg = plus.messaging.createMessage(plus.messaging.TYPE_EMAIL);
	// msg.to = ['2858652504@qq.com'];
	// msg.from = ['15321898686@163.com']
	// msg.subject = '测试邮件11';
	// msg.body = 'This is Pandora example test message';
	// plus.messaging.sendMessage(msg, function() {
	// 	alert("Send success!");
	// }, function() {
	// 	alert("Send failed!");
	// });
	// 扩展API加载完毕，现在可以正常调用扩展API
	plus.contacts.getAddressBook(plus.contacts.ADDRESSBOOK_PHONE, function(addressbook) {
		// 可通过addressbook进行通讯录操作
		addressbook.find(["displayName", "phoneNumbers", 'addresses'], function(contacts) {
			state.tableData = [...contacts]
			uni.showModal({
				content: `${JSON.stringify(contacts[0])}`,
				showCancel: false
			});
			state.name = '22222222'
		}, function() {
			alert("error");
		}, {
			multiple: true
		});
	}, function(e) {
		uni.showModal({
			content: `${e.message}`,
			showCancel: false
		});
	});

	// const contactsModule = uni.requireNativePlugin('jushi-Contacts')
	// contactsModule.getAllContactInfo(res => {
	// 	console.log(JSON.parse())
	// })
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
