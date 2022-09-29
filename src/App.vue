<script setup lang="ts">
import { ref, transformVNodeArgs } from 'vue'

let id = 3
const inputText = ref('')

const todos = ref<{
	id: number,
	text: string,
	check: boolean
}[]>([])
const addItem = () => {
	const obj = { id: id++, text: inputText.value, check: false }
	todos.value.push(obj);
	inputText.value = ''
}

const removeItem = (todo: { id: number, text: string, check: boolean }) => {
	todos.value = todos.value.filter(elem => elem !== todo)
}

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

const removeChecked = () => {
	todos.value = todos.value.filter(elem => elem.check == false)
}

</script>

<template>
	<form @submit.prevent="addItem" class="my-6">
		<input type="text" v-model='inputText' class="border-2 rounded-lg mx-6" id="add-item-input"
			autocomplete="off" />
		<button type="submit"
			class="bg-slate-500 rounded-full w-28 h-8 text-slate-50 hover:text-slate-500 hover:bg-slate-200 duration-200 border-2 border-slate-500">
			Add Todo
		</button>
	</form>
	<ul class="flex flex-col justify-center items-center">
		<li v-for="todo in todos" :key="todo.id" class="flex my-1 items-center">
			<input type="checkbox" v-model="todo.check" class="mx-2" />
			<input :value="todo.text" readonly class="outline-none border-transparent border-2 rounded-lg">
			<button @click="removeItem(todo)" class="mx-6 text-red-700">X</button>
			<button class="text-green-600" @click="(event) => renameItem(event, todo)">{{'<>'}}</button>
		</li>
	</ul>
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
