<template>
	<div class="container">
		<div class="logo" title="To Do App">
			To Do App
		</div>
		<todo-list></todo-list>
	</div>
</template>

<script>
	import TodoList from "./components/TodoList.vue";

	export default {
		name: "App",
		components: {
			TodoList,
		},
	};
</script>

<style>
	@import url("https://fonts.googleapis.com/css2?family=Josefin+Sans&family=Allan:wght@700&display=swap");

	* {
		box-sizing: border-box;
		font-family: "Josefin Sans", sans-serif;
	}

	body {
		min-height: 100vh;
		background-color: #2c2c2c;
		margin: 0;
	}

	#app {
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		color: #2c3e50;
		/* margin: 60px 0; */
		min-height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.container {
		width: 600px;
		margin: 0 auto;
		color: white;
		backdrop-filter: blur(7.5px);
		border: 5px solid white;
		border-radius: 15px;
		padding: 25px 0 50px 0;
	}

	.logo {
		font-weight: bold;
		text-align: center;
		font-size: 5em;
		font-family: "Allan", cursive;
		margin: 25px 0;
		color: dodgerblue;
	}

	@media screen and (max-width: 650px) {
		.container {
			width: 95%;
		}
	}
</style>
