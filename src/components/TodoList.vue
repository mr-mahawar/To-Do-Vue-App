<template>
	<div class="app-container">
		<input
			type="text"
			class="todo-input"
			placeholder="Enter New Task Here"
			v-model="newTask"
			@keyup.enter="addTask"
		/>
		<transition-group
			enter-active-class="animated fadeInUp"
			leave-active-class="animated fadeOutDown"
		>
			<div
				v-for="(task, index) in filteredTasksList"
				:key="task.id"
				class="todo-item"
			>
				<div class="todo-item-left">
					<input type="checkbox" v-model="task.completed" />
					<div
						v-if="!task.editing"
						@dblclick="editTask(task)"
						class="task-label"
						:class="{ completed: task.completed }"
					>
						{{ task.title }}
					</div>
					<input
						v-else
						class="task-edit"
						type="text"
						v-model="task.title"
						@blur="doneEditing(task)"
						@keyup.enter="doneEditing(task)"
						@keyup.esc="cancelEditing(task)"
						v-focus
					/>
				</div>
				<div class="remove-item" @click="removeTask(index)">
					&times;
				</div>
			</div>
		</transition-group>
		<div class="extra-container">
			<div>
				<label>
					<input
						ref="checkAllBox"
						type="checkbox"
						:checked="!anyRemaining"
						@change="checkAllTasks"
					/>
					Check All
				</label>
			</div>
			<div>{{ remaining }} items left</div>
		</div>
		<div class="extra-container">
			<div>
				<button
					:class="{ active: filter == 'all' }"
					@click="filter = 'all'"
				>
					All
				</button>
				<button
					:class="{ active: filter == 'active' }"
					@click="filter = 'active'"
				>
					Active
				</button>
				<button
					:class="{ active: filter == 'completed' }"
					@click="filter = 'completed'"
				>
					Completed
				</button>
			</div>
			<div>
				<transition name="fade">
					<button
						v-if="showClearCompletedButton"
						@click="clearCompleted"
					>
						Clear Completed
					</button>
				</transition>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: "todo-list",

		data() {
			return {
				newTask: "",
				idForTask: 2,
				tasksList: [],
				cachedTitle: "",
				filter: "all",
			};
		},

		directives: {
			focus: {
				// directive definition
				inserted: function(el) {
					el.focus();
				},
			},
		},

		computed: {
			remaining() {
				if (this.tasksList.length == 0) {
					return 0;
				}
				return this.tasksList.filter((task) => !task.completed).length;
			},

			anyRemaining() {
				return this.remaining != 0;
			},

			filteredTasksList() {
				if (this.filter == "all") {
					return this.tasksList;
				} else if (this.filter == "active") {
					return this.tasksList.filter((task) => !task.completed);
				} else if (this.filter == "completed") {
					return this.tasksList.filter((task) => task.completed);
				}

				return this.tasksList;
			},

			showClearCompletedButton() {
				return (
					this.tasksList.filter((task) => task.completed).length > 0
				);
			},
		},

		methods: {
			addTask() {
				if (this.newTask.trim().length == 0) {
					return;
				}

				this.tasksList.push({
					id: this.idForTask,
					title: this.newTask,
					completed: false,
					editing: false,
				});
				this.newTask = "";
				this.idForTask++;
			},

			removeTask(index) {
				this.tasksList.splice(index, 1);
			},

			editTask(task) {
				this.cachedTitle = task.title;
				task.editing = true;
			},

			doneEditing(task) {
				if (task.title.trim() == "") {
					task.title = this.cachedTitle;
				}
				task.editing = false;
				localStorage.setItem("tasks", JSON.stringify(this.tasksList));
			},

			cancelEditing(task) {
				task.title = this.cachedTitle;
				task.editing = false;
			},

			checkAllTasks() {
				this.tasksList.forEach((task) => {
					task.completed = event.target.checked;
				});
			},

			clearCompleted() {
				this.tasksList = this.tasksList.filter(
					(task) => !task.completed
				);

				if (this.tasksList.length == 0) {
					this.$refs.checkAllBox.checked = false;
				}
			},
		},

		beforeMount() {
			let tempData;
			if (localStorage.tasks) {
				this.tasksList = JSON.parse(localStorage.getItem("tasks"));
			} else {
				tempData = [
					{
						id: 1,
						title: "Create Todo App with Vue",
						completed: false,
						editing: false,
					},
				];
				localStorage.setItem("tasks", JSON.stringify(tempData));
				this.tasksList = JSON.parse(localStorage.getItem("tasks"));
			}
		},

		mounted() {
			if (this.tasksList.length == 0 && !this.anyRemaining) {
				this.$refs.checkAllBox.checked = false;
			}
		},

		updated() {
			localStorage.setItem("tasks", JSON.stringify(this.tasksList));
			if (this.tasksList.length == 0 && !this.anyRemaining) {
				this.$refs.checkAllBox.checked = false;
			}
		},
	};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
	.app-container {
		width: 90%;
		margin: 0 auto;
	}

	input[type="text"] {
		border-radius: 3px;
	}

	.todo-input {
		margin-bottom: 12px;
		width: 100%;
		border-radius: 0;
		border: 1px solid grey;
		padding: 10px;
		font-size: 0.9em;
		outline: none;
		transition: 200ms;
	}

	.todo-input:focus {
		background-color: #2c2c2c;
		color: white;
	}

	.todo-input:focus::placeholder {
		color: white;
	}

	.todo-item {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 0 auto 12px auto;
		font-size: 1em;
		min-height: 25px;
		width: 97.5%;
		animation-duration: 300ms;
	}

	.remove-item {
		cursor: pointer;
		font-size: 2em;
		transition: 200ms;
		user-select: none;
	}

	.remove-item:hover {
		transform: scale(1.2);
		color: rgb(255, 0, 85);
	}

	.todo-item-left {
		display: flex;
		align-items: center;
		width: 95%;
	}

	.task-label {
		max-width: 85%;
		padding: 5px;
		overflow-wrap: break-word;
	}

	.task-edit {
		padding: 4px;
		font-size: 1em;
		max-width: 85%;
		outline: none;
		transition: 200ms;
		border-radius: 0;
		border: 1px solid grey;
		overflow-wrap: break-word;
	}

	.task-edit:focus {
		background-color: #2c2c2c;
		color: white;
	}

	.task-edit:focus::placeholder {
		color: white;
	}

	.completed {
		text-decoration: line-through;
		color: grey;
	}

	.extra-container {
		display: flex;
		justify-content: space-between;
		align-items: center;
		border-top: 1px solid grey;
		padding: 10px 5px;
		font-size: 0.8em;
		user-select: none;
	}

	button {
		font-size: 0.95em;
		background-color: white;
		outline: none;
		border: 1px solid grey;
		padding: 5px 5px 3px 5px;
		margin: 0 5px;
		min-width: 40px;
		cursor: pointer;
		transition: 200ms;
		border-radius: 3px;
	}

	button:hover {
		background-color: rgb(149, 199, 250);
	}

	button:focus {
		box-shadow: 0 0 5px rgba(128, 128, 128, 0.25);
	}

	input[type="checkbox"] {
		cursor: pointer;
		transition: 200ms;
	}

	input[type="checkbox"]:hover {
		filter: brightness(0.75);
	}

	.extra-container label {
		cursor: pointer;
	}

	.active {
		background: dodgerblue;
	}

	/* CSS Transitions */
	.fade-enter-active,
	.fade-leave-active {
		transition: opacity 200ms;
	}

	.fade-enter,
	.fade-leave-to {
		opacity: 0;
	}
</style>
