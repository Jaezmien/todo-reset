<template>
	<div
		id="item"
		@dblclick="$emit('updateToDo')"
		:class="{
			completed: todo.completed,
		}"
	>
		<div id="details">
			<h2>{{ todo.name }}</h2>
			<p>Resets {{ get_readable_schedule().toLowerCase() }}</p>
		</div>
		<div id="delete" @click.stop="$emit('deleteToDo')">
			<div>
				<i>Delete</i>
			</div>
		</div>
	</div>
</template>

<script setup>
const props = defineProps({
	todo: Object,
})

function get_readable_schedule() {
	switch (props.todo.schedule) {
		case '1d':
			return 'Everyday'
		case '2d':
			return 'Every other day'
		case '3d':
			return 'Every three days'
		case '4d':
			return 'Every four days'
		case '5d':
			return 'Every five days'
		case '6d':
			return 'Every six days'
		case '1w':
			return 'Every week'
		case '2w':
			return 'Every other week'
		case '3w':
			return 'Every third week'
		case '1m':
			return 'Every month'
	}
}
</script>

<style scoped>
#item {
	position: relative;
	border-radius: 1em;
	transition: background 100ms ease-in-out, box-shadow 200ms ease-in-out;
	overflow: hidden;
	display: grid;
	grid-template-areas: 'details delete';
	grid-template-columns: auto minmax(auto, 6rem);
	place-items: center;
	min-height: 8rem;

	box-shadow: 0px 6px 8px 4px rgba(0, 0, 0, 0.2);
}
#item:hover {
	box-shadow: 0px 8px 10px 6px rgba(0, 0, 0, 0.15);
}
.completed {
	background-color: #aed581;
}
#details {
	grid-area: details;
}
#delete {
	grid-area: delete;
	user-select: none;
	cursor: pointer;
	width: 100%;
	height: 100%;
	background-color: white;
	transition: background 100ms ease-in-out, color 100ms ease-in-out, border 100ms ease-in-out;
	border-left: 2px lightgray solid;
}
#delete:hover {
	background-color: #b71c1c;
	color: #eee;
	border-left: 2px transparent solid;
}
#delete > div {
	display: grid;
	place-items: center;
	height: 100%;
}

h2 {
	word-wrap: break-word;
	padding-left: 1em;
	padding-right: 1em;
}
</style>