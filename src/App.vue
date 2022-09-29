<script setup lang="ts">
import { ref, transformVNodeArgs } from 'vue'

let id = 1
const inputText = ref('')
const inputTime = ref()
const inputDuration = ref(0)

// Store All Todos
const todos = ref<{
	id: number,
	text: string,
	time: Date,
	duration: number
	check: boolean
}[]>([])

// Function to add Todo in Todo List
const addItem = () => {
	const obj = { id: id++, text: inputText.value, time: inputTime.value as Date, duration: inputDuration.value as number, check: false }
	todos.value.push(obj);
	inputText.value = ''
	inputTime.value = null
	inputDuration.value = 0
	todos.value.sort((a, b) => a.time > b.time ? 1 : -1)
}

// Remove Todo button
const removeItem = (todo: { id: number, text: string, check: boolean }) => {
	todos.value = todos.value.filter(elem => elem !== todo)
}

// Rename Todo button
const renameItem = (event: MouseEvent, todo: { id: number, text: string }) => {
	const btn = event.target as HTMLElement
	const pElem = btn.parentElement
	const inp = pElem?.getElementsByTagName('input')[1]
	if (btn?.innerText == '<>') {
		btn.innerText = '+'
		inp?.removeAttribute('readonly')
		inp?.focus();
		inp?.classList.remove('border-transparent')
	}
	else if (btn?.innerText == '+') {
		btn.innerText = '<>'
		inp?.setAttribute('readonly', 'readonly')
		inp?.classList.add('border-transparent')
	}
}

// remove Checked Todo Button
const removeChecked = () => {
	todos.value = todos.value.filter(elem => elem.check == false)
}

console.log(new Date().getTime() / (1000 * 60))

</script>

<template>
	<!-- ADD TODO -->
	<form @submit.prevent="addItem" class="my-6 grid grid-cols-6 gap-4">
		<input type="text" v-model='inputText' class="border-2 rounded-lg col-start-3 col-end-5" id="add-item-input"
			autocomplete="off" />
		<button type="submit"
			class="bg-slate-500 rounded-full w-28 h-8 text-slate-50 hover:text-slate-500 hover:bg-slate-200 duration-200 border-2 border-slate-500">
			Add Todo
		</button>
		<input v-model='inputTime' type="datetime-local" class="border-2 rounded-lg col-start-4 col-end-5" />
		<input v-model='inputDuration' type="number" class="border-2 rounded-lg col-start-4 col-end-5" />
	</form>

	<!-- TODO LIST  -->
	<ul class="flex flex-col justify-center items-center">
		<li v-for="todo in todos" :key="todo.id" class="flex my-1 items-center">
			<input type="checkbox" v-model="todo.check" class="mx-2" />
			<input :value="todo.text" readonly class="outline-none border-transparent border-2 rounded-lg">
			<input :value="todo.time" readonly class="outline-none border-transparent border-2 rounded-lg">
			<input :value="todo.duration" readonly class="outline-none border-transparent border-2 rounded-lg">
			<button @click="removeItem(todo)" class="mx-6 text-red-700">X</button>
			<button class="text-green-600" @click="(event) => renameItem(event, todo)">{{'<>'}}</button>
		</li>
	</ul>

	<!-- REMOVE BUTTONS  -->
	<button @click="removeChecked"
		class="bg-red-700 mx-2 my-10 text-slate-50 w-40 h-8 rounded-full hover:text-red-700 border-2 border-red-700 hover:bg-slate-200 duration-200">
		Remove Checked
	</button>
	<button @click="() => {todos = []}"
		class="bg-red-700 text-slate-50 w-32 h-8 rounded-full hover:text-red-700 border-2 border-red-700 hover:bg-slate-200 duration-200">
		Remove All
	</button>
</template>

<style scoped>

</style>
