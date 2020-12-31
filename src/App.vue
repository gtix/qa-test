<template>
	<div id="app">
		<h1>Примеры</h1>
		<div class="tests">
			<test
				v-for="(test, index) in tests"
				:key="index"
				:title="test.title"
				:questions="test.questions"
				:mode="test.mode"
				:timer="test.timer"
				:shuffle-questions="test.shuffle_questions"
				:shuffle-answers="test.shuffle_answers"
				@onFinal="saveResult"
			/>
		</div>
	</div>
</template>

<script>
import Test from "./components/Test.vue";
import _tests from "./assets/tests.json";
export default {
	components: { Test },
	name: "App",
	data() {
		return {
			tests: [],
		};
	},

	methods: {
		saveResult(results) {
			console.log(results);
		},
	},

	created() {
		this.tests = _tests;
	},
};
</script>

<style lang="scss">
body {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	background: #f0f2f5;
	font-size: 14px;
	color: #2c3e50;
}
#app {
	margin-top: 60px;
}
h1 {
	text-align: center;
}
.tests {
	max-width: 1200px;
	margin: 0 auto;
	width: 100%;
}
</style>
