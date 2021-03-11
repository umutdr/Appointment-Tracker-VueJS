<template>
	<header>
		<h1>{{ title }}</h1>
		<Button
			@btn-click="$emit('toggle-add-task-form')"
			:text="showAddTaskForm ? 'Close' : 'Add Task'"
			:color="showAddTaskForm ? 'orange' : 'green'"
		/>
	</header>
	<small>Tip: Double click a task to toggle reminder.</small>
</template>

<script>
import Button from './Button';

export default {
	name: 'Header',
	props: {
		title: {
			type: String,
			default: 'Default Title',
		},
		showAddTaskForm: Boolean,
	},
	components: {
		Button,
	},
};
</script>

<style scoped>
header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 20px;
}
</style>
