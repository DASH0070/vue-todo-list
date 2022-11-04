<script setup lang="ts">
import { ref, onMounted } from 'vue'


let id = 1
const inputText = ref('')
const inputTime = ref('')
const inputDuration = ref()

// Store All Todos
const todos = ref<{
	id: number,
	text: string,
	time: string,
	duration: number,
	check: boolean
}[]>([])

// Function to add Todo in Todo List
const addItem = () => {
	const dt = new Date(inputTime.value)
	// Form Validation
	if (inputText.value === '') {
		alert('Add some todo')
		return
	}
	if (dt.getTime() < new Date().getTime()) {
		alert('This time has passed')
		return
	}
	if (inputDuration.value < 1) {
		alert('Duration Not Proper')
		return
	}
	// If no ELEMENTS in Todo
	if (todos.value.length == 0) {
		todos.value.push({ id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false })
		return
	}
	// If Start is after Last Todo
	if (dt.getTime() >= new Date(todos.value[todos.value.length - 1].time).getTime() + todos.value[todos.value.length - 1].duration * 60000) {
		todos.value.push({ id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false })
		// inputText.value = ''
		// inputTime.value = ''
		// inputDuration.value = 0
		return
	}
	// If End is before first Todo
	if (dt.getTime() + inputDuration.value * 60000 <= new Date(todos.value[0].time).getTime()) {
		todos.value.unshift({ id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false })
		return
	}

	// Handling Time Conflict
	let st = 0, end = todos.value.length - 1
	for (let i = 0; i < todos.value.length; i++) {
		const currDt = new Date(todos.value[i].time)
		// start Time after current Todo
		if (dt.getTime() >= currDt.getTime()) {
			st = i
		}
		// start time after end current todo
		if ((dt.getTime() >= currDt.getTime() + todos.value[i].duration * 60000)) {
			st = i + 1
		}
		// End Time after start of Current Todo
		if (dt.getTime() + inputDuration.value * 60000 > currDt.getTime()) {
			end = i
		}
	}
	if (end < st)
		end = st
	if (st == end && new Date(todos.value[st].time).getTime() >= dt.getTime() + inputDuration.value * 60000) {
		for (let i = todos.value.length; i > st; i--) {
			todos.value[i] = todos.value[i - 1]
			todos.value[st] = { id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false }
			return
		}
		todos.value[st] = { id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false }
		return
	}
	// Asking for Confirmation To remove Todo
	console.log(st, end)
	if ((confirm('There are already some Todo Do you want to remove them'))) {
		if (st == end && new Date(todos.value[st].time).getTime() < dt.getTime() + inputDuration.value * 60000) {
			todos.value[st] = { id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false }
			return
		}
		if (end == todos.value.length - 1) {
			for (let i = st; i < end; i++) {
				todos.value.pop()
			}
			todos.value[st] = { id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false }
			return
		}
		let i = 0
		for (i = st + 1; i < todos.value.length - end; i++) {
			todos.value[i] = todos.value[i + end - st]
		}
		console.log(i)
		for (let j = todos.value.length; j > i; j--) {
			todos.value.pop()
		}
		todos.value[st] = { id: id++, text: inputText.value, time: inputTime.value, duration: inputDuration.value as number, check: false }
	}
	else {
		alert('No Todo Deleted Give Todo Not Added')
	}
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
		inp?.focus()
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

// console.log(new Date().getTime() / (1000 * 60))

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
		<input v-model="inputTime" required type="datetime-local" class="border-2 rounded-lg col-start-4 col-end-5" />
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
			<button class="text-green-600" @click="(event) => renameItem(event, todo)">{{         '<>'         }}</button>
		</li>
	</ul>

	<!-- REMOVE BUTTONS  -->
	<button @click="removeChecked"
		class="bg-red-700 mx-2 my-10 text-slate-50 w-40 h-8 rounded-full hover:text-red-700 border-2 border-red-700 hover:bg-slate-200 duration-200">
		Remove Checked
	</button>
	<button @click="() => {         todos = []         }"
		class="bg-red-700 text-slate-50 w-32 h-8 rounded-full hover:text-red-700 border-2 border-red-700 hover:bg-slate-200 duration-200">
		Remove All
	</button>
</template>

<style scoped>

</style>
