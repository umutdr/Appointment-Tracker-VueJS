<template>
	<div class="container">
		<Header
			@toggle-add-task-form="toggleAddTaskForm"
			title="Task Tracker"
			:showAddTaskForm="showAddTaskForm"
		/>
		<div v-show="showAddTaskForm">
			<AddTask @add-task="addTask" />
		</div>
		<Tasks
			@delete-task="deleteTask"
			@toggle-reminder="toggleReminder"
			:tasks="tasks"
		/>
	</div>
</template>

<script>
import Header from './components/Header';
import Tasks from './components/Tasks';
import AddTask from './components/AddTask';

export default {
	name: 'App',
	components: {
		Header,
		Tasks,
		AddTask,
	},
	data() {
		return {
			tasks: [],
			showAddTaskForm: false,
		};
	},
	methods: {
		async addTask(task) {
			const response = await fetch('api/tasks', {
				method: 'POST',
				headers: {
					'Content-type': 'application/json',
				},
				body: JSON.stringify(task),
			});

			const data = await response.json();

			this.tasks = [...this.tasks, data];
		},
		async deleteTask(taskId) {
			if (confirm('Are you sure to delete this task')) {
				const response = await fetch(`api/tasks/${taskId}`, {
					method: 'DELETE',
				});

				let httpOKResponseStatusCode = 200;
				response.status === httpOKResponseStatusCode
					? (this.tasks = this.tasks.filter((task) => task.id !== taskId))
					: alert('Delete is Failed!');
			}
		},
		async toggleReminder(taskId) {
			const tastkToToggleReminder = await this.fetchTask(taskId);
			const updatedTask = {
				...tastkToToggleReminder,
				reminder: !tastkToToggleReminder.reminder,
			};
			const response = await fetch(`api/tasks/${taskId}`, {
				method: 'PUT',
				headers: {
					'Content-type': 'application/json',
				},
				body: JSON.stringify(updatedTask),
			});

			const data = await response.json();

			this.tasks = this.tasks.map((task) =>
				task.id === taskId ? { ...task, reminder: data.reminder } : task
			);
		},
		toggleAddTaskForm() {
			this.showAddTaskForm = !this.showAddTaskForm;
		},
		async fetchTasks() {
			const response = await fetch('api/tasks');

			return await response.json();
		},
		async fetchTask(taskId) {
			const response = await fetch(`api/tasks/${taskId}`);

			return await response.json();
		},
	},
	async created() {
		this.tasks = await this.fetchTasks();
	},
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');

* {
	box-sizing: border-box;
	margin: 0;
	padding: 0;
}

body {
	font-family: 'Poppins', sans-serif;
}

.container {
	max-width: 500px;
	margin: 30px auto;
	overflow: auto;
	min-height: 300px;
	border: 1px solid steelblue;
	padding: 30px;
	border-radius: 5px;
}

.btn {
	display: inline-block;
	background: #000;
	color: #fff;
	border: none;
	padding: 10px 20px;
	margin: 5px;
	border-radius: 5px;
	cursor: pointer;
	text-decoration: none;
	font-size: 15px;
	font-family: inherit;
}

.btn:focus {
	outline: none;
}

.btn:active {
	transform: scale(0.98);
}

.btn-block {
	display: block;
	width: 100%;
}
</style>
