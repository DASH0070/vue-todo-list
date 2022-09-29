<script setup lang="ts">
import { ref } from 'vue'

let id = 3
const inputText = ref('')

const todos = ref<{
	id: number,
	text: string
}[]>([])
const addItem = () => {
	const obj = { id: id++, text: inputText.value }
	todos.value.push(obj);
	inputText.value = ''
}

const removeItem = (todo: { id: number, text: string }) => {
	todos.value = todos.value.filter(elem => elem !== todo)
}

const renameItem = (event: MouseEvent, todo: { id: number, text: string }) => {
	const btn = event.target as HTMLElement
	const pElem = btn.parentElement
	const inp = pElem?.getElementsByTagName('input')[0]
	if (btn?.innerText == '<>') {
		btn.innerText = '+'
		inp?.removeAttribute('readonly')
		inp?.focus();
		inp?.classList.add('border-2', 'rounded-lg')
	}
	else if (btn?.innerText == '+') {
		btn.innerText = '<>'
		inp?.setAttribute('readonly', 'readonly')
		inp?.classList.remove('border-2', 'rounded-lg')
	}
}

</script>

<template>
	<form @submit.prevent="addItem" class="my-6">
		<input type="text" v-model='inputText' class="border-2 rounded-lg mx-6" id="add-item-input" />
		<button type="submit"
			class="bg-slate-500 rounded-full w-28 h-8 text-slate-50 hover:text-slate-500 hover:bg-slate-50">Add
			Todo</button>
	</form>
	<ul class="flex flex-col justify-center items-center">
		<li v-for="todo in todos" :key="todo.id" class="flex my-1">
			<input :value="todo.text" readonly class="outline-none">
			<button @click="removeItem(todo)" class="mx-6 text-red-700">X</button>
			<button class="text-green-600" @click="(event) => renameItem(event, todo)">{{'<>'}}</button>
		</li>
	</ul>
</template>

<style scoped>

</style>
