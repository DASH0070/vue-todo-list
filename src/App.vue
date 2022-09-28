<script setup lang="ts">
import { ref } from 'vue'

let id = 3
const inputText = ref('')

const todos = ref([
	{ id: 0, text: 'Apple' },
	{ id: 1, text: 'Mango' },
	{ id: 2, text: 'Orange' }
])
const addItem = () => {
	const obj = { id: id++, text: inputText.value }
	todos.value.push(obj);
	inputText.value = ''
}

const removeItem = (todo: object) => {
	todos.value = todos.value.filter(elem => elem !== todo)
}

</script>

<template>
	<form @submit.prevent="addItem" class="object-center">
		<input type="text" v-model='inputText' class="border-2"/>
		<button type="submit">Add Todo</button>
	</form>
	<ul class="flex flex-col justify-center items-center">
		<li v-for="todo in todos" :key="todo.id" class="flex" >
			<p>{{todo.text}}</p>
			<button @click="removeItem(todo)" class="w-40">X</button>
		</li>
	</ul>
</template>

<style scoped>

</style>
