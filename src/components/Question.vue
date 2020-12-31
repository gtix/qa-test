<template>
	<div class="test__question">
		<div class="test__question-title">{{ title }}</div>
		<div>
			<div
				v-for="(answer, index) in answers"
				:key="index"
				class="test__question-answer"
			>
				<input
					v-if="mode === 'single'"
					:id="hash_answer + index"
					type="radio"
					:value="answer"
					v-model="user_answer"
					class="test__question-answer-radio"
				/>
				<input
					v-else
					:id="hash_answer + index"
					type="checkbox"
					:value="answer"
					v-model="user_answer"
					class="test__question-answer-checkbox"
				/>
				<label :for="hash_answer + index" class="test__question-answer-label">
					<span class="test__question-answer-text">{{ answer.text }}</span>
				</label>
			</div>
		</div>
		<div v-if="showAnswerWarning" class="test__question-answer-warning">
			Выберите ответ!
		</div>
	</div>
</template>

<script>
export default {
	props: {
		title: String,
		answers: {
			type: Array,
			required: true,
		},
		showAnswerWarning: {
			type: Boolean,
			default: false,
		},
		mode: {
			type: String,
			default: "single",
			validator: function(value) {
				return ["single", "multiply"].indexOf(value) !== -1;
			},
		},
		hash: {
			type: [String, Number],
		},
	},

	data() {
		return {
			hash_answer: "",
			result: undefined,
		};
	},

	computed: {
		user_answer: {
			get() {
				return this.result;
			},
			set(answer) {
				this.result = answer;
				this.$emit("updateResult", this.hash, this.result);
			},
		},
	},
	methods: {
		generateHash() {
			var rnd = "";
			while (rnd.length < 4)
				rnd += Math.random()
					.toString(36)
					.substring(2);
			return rnd.substring(0, 4);
		},
		init() {
			this.hash_answer = this.generateHash();
			if (this.mode === "single" && this.result === undefined) {
				this.result = undefined;
			} else if (this.mode === "multiply" && this.result === undefined) {
				this.result = [];
			}
		},
	},
	created() {
		this.init();
	},
};
</script>

<style lang="scss" scoped>
.test__question {
	margin: 5px 0 10px;

	& ~ & {
		margin-top: 20px;
	}
	&-title {
		color: #051f3c;
		font-size: 16px;
	}
	&-answer {
		margin: 10px 0;
		&-warning {
			background: #fffbe6;
			border: 1px solid #ffe58f;
			border-radius: 5px;
			font-size: 15px;
			padding: 10px;
			color: #000;
			margin: 15px auto 15px;
		}
		&-radio,
		&-checkbox {
			display: none;
		}
		&-label {
			display: inline-block;
			cursor: pointer;
			position: relative;
			padding-left: 25px;
			margin: 0;
			user-select: none;
			font-size: 14px;
		}
		&-radio + &-label:before {
			content: "";
			position: absolute;
			top: calc((100% - 17px) / 2);
			left: 0;
			height: 13px;
			width: 13px;
			background-color: #f1f2f4;
			border: 1px solid #e3e6ec;
			border-radius: 50%;
		}
		&-checkbox + &-label:before {
			content: "";
			position: absolute;
			top: calc((100% - 17px) / 2);
			left: 0;
			height: 13px;
			width: 13px;
			background-color: #f1f2f4;
			border: 1px solid #e3e6ec;
			border-radius: 5px;
		}
		&-radio:checked + &-label:before,
		&-checkbox:checked + &-label:before {
			background-color: #008bff;
			border-color: #008bff;
		}
		&-label:hover:before {
			background-color: #e3e6ec;
		}
	}
}
</style>
