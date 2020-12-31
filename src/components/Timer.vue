<template>
	<div class="test__timer" v-if="timer_counter >= 0">
		<progress
			class="test__timer-progress"
			:value="timer_counter"
			:max="timer"
		></progress>
		<div class="test__timer-counter">
			<div class="test__timer-counter-num">{{ timer_counter }}</div>
			<div class="test__timer-counter-title">Таймер</div>
		</div>
	</div>
</template>

<script>
export default {
	props: {
		timer: {
			type: Number,
			default: 60,
		},
		nextQuestion: {
			type: Function,
			required: true,
		},
	},
	data() {
		return {
			timer_counter: 0,
			timer_interval: null,
		};
	},
	computed: {
		progress() {
			return this.timer - this.timer_counter;
		},
	},
	methods: {
		start() {
			clearInterval(this.timer_interval);
			this.timer_counter = this.timer;

			this.timer_interval = setInterval(() => {
				if (this.timer_counter) {
					// Уменьшаем таймер
					this.timer_counter--;
				} else {
					// Следующий вопрос
					if (!this.nextQuestion(true)) {
						clearInterval(this.timer_interval);
					}
				}
			}, 1000);
		},
		restart() {
			this.timer_counter = this.timer;
		},
		off() {
			this.timer_counter = -1;
			clearInterval(this.timer_interval);
		},
	},
	beforeDestroy() {
		clearInterval(this.timer_interval);
	},
};
</script>

<style lang="scss" scoped>
.test__timer {
	height: 5px;
	width: 100%;
	position: relative;
	display: flex;
	justify-content: space-between;
	align-items: flex-start;

	::-webkit-progress-bar {
		background: #fff;
	}
	::-webkit-progress-value {
		background: #008bff;
	}
	::-moz-progress-bar {
		background: #008bff;
	}
	&-progress {
		display: block;
		background: #008bff;
		color: #000;
		width: 100%;
		height: 5px;
		box-sizing: border-box;
		transition: all 1s linear;
	}
	&-counter {
		display: block;
		text-align: center;
		background: #fff;
		border-radius: 0 0 0 5px;
		padding: 5px 10px;
		&-num {
			font-size: 16px;
			font-weight: bold;
		}
		&-title {
			font-size: 12px;
		}
	}
}
</style>
