<template>
	<view>
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,i) in swiperList" :key="i">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goodes_id">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<view class="nav-list">
			<view class="nav-item" v-for="(item,i) in navList" :key="i" @click="navClickHandler(item)">
				<image :src="item.image_src" class="nav-img"></image>
			</view>
		</view>

		<view class="floor">
			<view class="floor-item" v-for="(item,i) in floorList" :key="i">
				<image :src="item.floor_title.image_src" class="floor-title"></image>
				<view class="box">
					<navigator class="floor-lift" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" class="floor-lift-img" mode="widthFix"></image>
					</navigator>
					<view class="floor-right">
						<navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2"
							:url="item2.url">
							<view v-if=" i2 !==0">
								<image :src="item2.image_src" mode="widthFix"
									:style="{width: item2.image_width + 'rpx'}">
								</image>
							</view>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperList: [],
				navList: [],
				floorList: []
			}
		},
		onLoad() {
			this.getSwiperList()
			this.getnavList()
			this.getfloorList()
		},
		methods: {
			async getSwiperList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/swiperdata')
				if (res.meta.status != 200) return uni.$showMsg()
				this.swiperList = res.message
			},
			async getnavList() {
				const {
					data: res1
				} = await uni.$http.get('/api/public/v1/home/catitems')
				if (res1.meta.status != 200) return uni.$showMsg()
				this.navList = res1.message
			},
			navClickHandler(item) {
				// 判断点击的是哪个 nav
				if (item.name === '分类') {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			},
			async getfloorList() {
				const {
					data: res2
				} = await uni.$http.get('/api/public/v1/home/floordata')
				if (res2.meta.status != 200) return uni.$showMsg()

				res2.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					})
				})

				this.floorList = res2.message
			},
		}

	}
</script>

<style lang="scss">
	swiper {
		height: 330rpx;

		.swiper-item,
		image {
			width: 100%;
			height: 100%;
		}
	}

	.nav-list {
		display: flex;
		justify-content: space-around;
		margin: 15px 0;

		.nav-img {
			width: 128rpx;
			height: 140rpx;
		}
	}

	.floor {

		//标题
		.floor-title {
			height: 60rpx;
			width: 100%;
			display: flex;
		}

		//左图
		.floor-lift-img {
			width: 233rpx;
		}

		.floor-right {
			float: left;
			display: flex;
			flex-wrap: wrap;
			justify-content: space-around;
		}

		.box {
			display: flex;
			padding-left: 10rpx;
		}
	}
</style>
