<script setup>
import ToDoItem from './components/ToDoItem.vue'
import dayjs from 'dayjs'
import { reactive } from 'vue'

let todos = reactive([])

function add_todo(el) {
	const data = {}
	for (const entry of new FormData(el.target).entries()) {
		data[entry[0]] = entry[1]
	}
	if (!data.todoTask.trim()) return
	const currentDate = dayjs().millisecond(0).second(0).minute(0).hour(0) // Starting at 12 AM

	todos.push({
		name: data.todoTask,
		schedule: data.todoSchedule,
		lastDate: currentDate.toISOString(),
		completed: false,
	})
	save_todos()

	el.target.reset()

	console.log('📒 Added todo item!')
}
function update_todo_state(index) {
	todos[index].completed = !todos[index].completed
	save_todos()
	console.log('✍ Updated todo item!')
}
function delete_todo(index) {
	todos.splice(index, 1)
	save_todos()
	console.log('🚮 Deleted todo item!')
}

function save_todos() {
	localStorage['todos'] = JSON.stringify(todos)
}
function load_previous_todos() {
	if (localStorage['todos']) todos.push(...JSON.parse(localStorage['todos']))

	const currentDate = dayjs().millisecond(0).second(0).minute(0).hour(0) // Starting at 12 AM
	for (const index in todos) {
		const todo = todos[index]
		if (!todo.completed) continue

		const sched = /(\d+)(\w+)/g.exec(todo.schedule)
		if (currentDate.diff(dayjs(todo.lastDate), sched[2]) >= parseInt(sched[1])) {
			todo.completed = false
			let last = dayjs(todo.lastDate)
			while (currentDate.diff(last, sched[2]) >= parseInt(sched[1])) {
				last = last.add(parseInt(sched[1]), sched[2])
			}
			todo.lastDate = last.toISOString()
		}
	}

	save_todos()
}
load_previous_todos()
</script>

<template>
	<h1>ToDo App</h1>
	<form id="input" @submit.prevent="add_todo">
		<input type="text" name="todoTask" placeholder="Task Name" id="todoTask" required />
		<select name="todoSchedule" id="todoSchedule">
			<option selected value="1d">Everyday</option>
			<option value="2d">Every other day</option>
			<option value="3d">Every three days</option>
			<option value="4d">Every four days</option>
			<option value="5d">Every five days</option>
			<option value="6d">Every six days</option>
			<option value="1w">Every week</option>
			<option value="2w">Every other week</option>
			<option value="3w">Every third week</option>
			<option value="1m">Every month</option>
		</select>
		<button id="todoAdd">Add</button>
	</form>
	<br />
	<div id="todos">
		<to-do-item
			v-for="(todo, index) in todos"
			:key="todo"
			:todo="todo"
			@update-to-do="update_todo_state(index)"
			@delete-to-do="delete_todo(index)"
		>
		</to-do-item>
		<p v-show="!todos.length">Your list is empty :)</p>
	</div>
</template>

<style lang="scss">
$MOBILE: 40rem;
$INPUTBORDER: lightgray;
$INPUTRADIUS: 0.4rem;

body {
	margin: 2rem;
}
#app {
	font-family: Roboto, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
#input {
	@media (min-width: $MOBILE) {
		display: flex;

		> * {
			box-sizing: border-box;
			border: 0;
			border-radius: 0;
			border-top: 1px $INPUTBORDER solid;
			border-bottom: 1px $INPUTBORDER solid;
			height: 3rem;
		}
		& > :first-child {
			border-left: 1px $INPUTBORDER solid;
			border-top-left-radius: $INPUTRADIUS;
			border-bottom-left-radius: $INPUTRADIUS;
		}
		& > :not(:first-child) {
			border-left: 1px $INPUTBORDER solid;
		}
		& > :last-child {
			border-right: 1px $INPUTBORDER solid;
			border-top-right-radius: $INPUTRADIUS;
			border-bottom-right-radius: $INPUTRADIUS;
		}

		#todoTask {
			flex-grow: 8;
		}
		#todoSchedule {
			flex-grow: 2;
		}
		#todoAdd {
			flex-grow: 1;
		}
	}
	@media (max-width: $MOBILE) {
		display: flex;
		flex-direction: column;
		> * {
			box-sizing: border-box;
			border: 0;
			border-radius: 0;
			border-left: 1px $INPUTBORDER solid;
			border-right: 1px $INPUTBORDER solid;
			height: 3rem;
		}
		& > :first-child {
			border-top: 1px $INPUTBORDER solid;
			border-top-left-radius: $INPUTRADIUS;
			border-top-right-radius: $INPUTRADIUS;
		}
		& > :not(:first-child) {
			border-top: 1px $INPUTBORDER solid;
		}
		& > :last-child {
			border-bottom: 1px $INPUTBORDER solid;
			border-bottom-left-radius: $INPUTRADIUS;
			border-bottom-right-radius: $INPUTRADIUS;
		}
	}

	#todoAdd {
		cursor: pointer;
		color: #111;
		background-color: #558b2f;
		&:hover {
			background-color: #33691e;
			color: #eee;
		}
	}
}
#todos {
	margin-top: 1rem;
	display: flex;
	flex-direction: column;
	gap: 1em;
}
</style>
