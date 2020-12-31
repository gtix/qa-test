<template>
	<div>
		<div class="test">
			<timer
				v-if="timer !== false"
				ref="timer"
				:timer="timer"
				:nextQuestion="nextQuestion"
			/>

			<div class="test__container" v-if="!show_result">
				<b
					class="test__container-counter"
					v-if="mode === 'single' && showCounter"
				>
					Вопрос: {{ counter }}/{{ questions.length }}
				</b>
				<h2 class="test__container-title" v-if="title">{{ title }}</h2>

				<question
					v-for="(question, index) in mode_questions"
					:key="index + counter"
					:title="question.title"
					:answers="question.answers"
					:mode="question.mode"
					:hash="question.hash"
					@updateResult="updateResult"
					:show-answer-warning="answer_warnings[question.hash]"
				/>
				<button
					v-if="mode === 'multiply' || is_last_question"
					@click="onFinal"
					class="test__container-button"
				>
					Завершить
				</button>
				<button v-else @click="nextQuestion" class="test__container-button">
					Ответить
				</button>
			</div>
			<div v-else class="test__result">
				<span class="test__result-title">Тест завершен</span>
			</div>
		</div>
	</div>
</template>

<script>
import Question from "./Question.vue";
import Timer from "./Timer.vue";
export default {
	components: { Question, Timer },
	props: {
		title: String,
		questions: {
			type: Array,
			required: true,
		},
		mode: {
			type: String,
			default: "multiply",
			validator: function(value) {
				return ["single", "multiply"].indexOf(value) !== -1;
			},
		},
		shuffleQuestions: {
			type: Boolean,
			default: false,
		},
		shuffleAnswers: {
			type: Boolean,
			default: false,
		},
		timer: {
			type: [Number, Boolean],
			default: false,
		},
		showCounter: {
			type: Boolean,
			default: true,
		},
	},
	data() {
		return {
			result: {},
			answer_warnings: {},
			counter: 1,
			show_result: false,
		};
	},

	computed: {
		current_question() {
			return this.questions[this.counter - 1];
		},
		is_last_question() {
			return this.counter === this.questions.length;
		},
		mode_questions() {
			return this.mode === "single" ? [this.current_question] : this.questions;
		},
	},

	methods: {
		updateResult(hash, answer) {
			// Обновляем результат ответа
			this.result = {
				...this.result,
				[hash]: answer,
			};
			this.validAnswers(hash);
		},
		onFinal() {
			// Завершение теста
			if (!this.validAnswers()) {
				return;
			}
			this.$emit("onFinal", this.result);
			if (this.timer !== false && this.mode === "single") {
				this.$refs.timer.off();
			}
			this.show_result = true;
		},
		nextQuestion(onTimer = false) {
			console.log("timer:", onTimer);
			// Переход к следующему вопросу (в single mode)
			// Валидация ответов
			const is_valid = this.validAnswers(this.current_question.hash);

			if (!onTimer && !is_valid) {
				// Ответ неправильный - ничего не возвращаем, вопрос не переключаем
				return;
			} else if (onTimer && !is_valid && !this.is_last_question) {
				// Оставляем ответ пустым, если функция вызвана по таймеру
				this.updateResult(this.current_question.hash, null);
			}

			// Если вопрос не последний - то переходим к следующему
			if (!this.is_last_question) {
				this.counter++;
				this.$refs.timer.restart();
				return true;
			}

			return false;
		},
		validAnswers(hash = null) {
			let is_valid = true;
			// Вопросы которые будем проверять на ошибки (наличие ответа)

			// Если режим нескольких вопросов - проверяем каждый вопрос
			// Если указан хеш вопроса, то проверяем только его
			const check_questions =
				hash === null
					? this.questions
					: [this.questions.find((q) => q.hash === hash)];

			let result_question = null;
			for (const question of check_questions) {
				result_question = this.result[question.hash];
				// Проверка на отсутствие ответа
				if (
					result_question === undefined ||
					(result_question instanceof Array && result_question.length === 0)
				) {
					this.setWarning(question.hash, true);
					is_valid = false;
				} else {
					this.setWarning(question.hash, false);
				}
			}
			return is_valid;
		},
		setWarning(hash, status) {
			// Предупреждения по вопросам
			// Если false - все хорошо
			// Если true - показать предупреждение: Выберите ответ!
			this.answer_warnings = {
				...this.answer_warnings,
				[hash]: status,
			};
		},
		init() {
			// Перемешиваем вопросы
			if (this.shuffleQuestions) {
				this.questions.sort(() => Math.random() - 0.5);
			}

			// Перемешиваем варианты ответов
			if (this.shuffleAnswers) {
				for (const index in this.questions) {
					this.questions[index].answers = this.questions[index].answers.sort(
						() => Math.random() - 0.5
					);
				}
			}
		},
	},
	created() {
		this.init();
	},
	mounted() {
		if (this.timer !== false && this.mode === "single") {
			this.$refs.timer.start();
		}
	},
};
</script>
<style lang="scss" scoped>
.test {
	background: #fff;
	box-sizing: border-box;
	border-radius: 5px;
	overflow: hidden;
	margin-bottom: 25px;
	&__container {
		padding: 15px 15px 30px;

		&-title {
			font-size: 20px;
			padding: 0 30px 0 0;
			box-sizing: border-box;
			margin: 0 0 15px;
		}
		&-counter {
			font-size: 14px;
		}

		&-button {
			width: 100%;
			padding: 10px;
			text-align: center;
			font-size: 16px;
			color: #fff;
			background: #008bff;
			border-radius: 5px;
			box-sizing: border-box;
			border: 0;
			outline: 0;
			margin: 10px auto 0;
			cursor: pointer;
			&:hover {
				background: #006bc3;
			}
		}
	}
	&__result {
		padding: 50px 0;
		&-title {
			font-size: 20px;
			text-align: center;
			display: block;
		}
	}
}
</style>
