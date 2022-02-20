<template>
	<view class="container">
		<div class="wrapper" @touchstart='handleTouchStart' @touchmove='onTouchMove' @touchend='onTouchEnd'>
			<block v-for="(item,index) in source" :key='item.id'>
				<view :class="['slide',renderSlide(index)]">
					<image :src="item.pic" class="image"></image>
				</view>
			</block>
		</div>
	</view>
</template>

<script>
	import source from './data.js'
	export default {
		data() {
			return {
				source,
				step: 1,
				current: null,
				start: {
					x: 0,
					y: 0
				},
				move: {
					x: 0,
					y: 0
				},
			}
		},
		watch: {
			move() {
				const move = this.move
				// 纵向滑动 不滑动
				if (Math.abs(move.y) > Math.abs(move.x)) {
					return
				}
				// 横向滑动 距离小于20不滑动
				if (Math.abs(move.x) < 20) {
					return
				}
				if (move.x <= 0) {

					console.log('左滑')
					this.current = true

				} else {
					console.log('右滑')
					this.current = false

				}
			}
		},
		methods: {
			next() {
				this.step = this.step + 1;
				if (this.step > this.source.length - 1) {
					this.step = 0
				}
			},
			prev() {
				this.step = this.step - 1;
				if (this.step < 0) {
					this.step = this.source.length - 1
				}
			},
			handleTouchStart(e) {
				this.start = {
					x: e.touches[0].pageX,
					y: e.touches[0].pageY
				}
			},
			onTouchMove(e) {
				let currentX = e.touches[0].pageX
				let currentY = e.touches[0].pageY
				this.move = {
					x: currentX - this.start.x,
					y: currentY - this.start.y
				}
			},
			onTouchEnd(e) {
				
				if (this.current === true) {
					this.throttle(this.next(), 1000)
					
				}
				if (this.current === false) {
					this.throttle(this.prev(), 1000)
				}
				this.current = null
			},
			throttle(fn, time) {
				let t = null
				return function() {
					if (t) {
						return
					}
					t = setTimeout(() => {
						// fn.call(this);
						fn()
						t = null
					}, time)
				}
			},
			renderSlide(index) {
				let len = this.source.length,
					temp1 = this.step - 1,
					temp2 = this.step,
					temp3 = this.step + 1;

				// 数组最后一位
				if (temp1 < 0) {
					temp1 = len + temp1
				}
				// 数组第一位
				if (temp3 > len - 1) {
					temp3 = temp3 - len
				}

				let className = 'temp'

				switch (index) {
					case temp2:
						className = 'temp2'
						break
					case temp1:
						className = 'temp1'
						break
					case temp3:
						className = 'temp3'
						break
				}
				return className
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		width: 100vw;
		height: 100vh;
		box-sizing: border-box;
		// border: 1px solid red;
		background: url('https://jingfentui.oss-cn-hangzhou.aliyuncs.com/2022/02/20/c074b3a5-5957-4d36-8075-f11029538a54.png') no-repeat;
		background-size: cover;
		position: relative;

		.wrapper {
			position: absolute;
			top: 45%;
			left: 50%;
			width: 90%;
			height: 842upx;
			transform: translate(-50%, -50%);
			opacity: 1;
			// border: 1px solid red;

			.slide {
				position: absolute;
				top: 50%;
				left: 50%;
				transform: translate(-50%, -50%);
				width: 574upx;
				height: 842upx;
				border-radius: 8upx;
				overflow: hidden;
				z-index: 0;
				transition: 0.5s;

				.image {
					width: 100%;
					height: 100%;
					display: block;
				}
			}

			.temp {
				transform: translate(-50%, -50%) scale(0.55);
				z-index: 0;
				filter: blur(5px);
			}

			.temp1 {
				transform: translate(-67%, -50%) scale(0.85);
				z-index: 1;
				filter: blur(5px);
			}

			.temp2 {
				transform: translate(-50%, -50%) scale(1);
				z-index: 2;
			}

			.temp3 {
				transform: translate(-32%, -50%) scale(0.85);
				z-index: 1;
				filter: blur(5px);
			}

		}
	}
</style>
