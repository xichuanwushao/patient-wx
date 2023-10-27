<template>
	<view>
		<view class="main">
			<view class="left" :style="height">
				<view
					:class="one.id == deptId ? 'item active' : 'item'"
					v-for="one in deptList"
					@tap="clickDeptHandle(one.id)"
				>
					{{ one.name }}
				</view>
			</view>
			<view class="right">
				<view class="item" v-for="one in sub" @tap="clickSubHandle(one.id, one.name)">{{ one.name }}</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			deptId: null,
			sub: [],
			height: 0,
			deptList: []
		};
	},
	methods: {
		
	},
	onLoad: function() {
		
	}
};
</script>

<style lang="less">
@import url(medical_dept_list.less);
</style>
