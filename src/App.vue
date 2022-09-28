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
	const obj = { id: id++, text: inputText.value}
	todos.value.push(obj);
	inputText.value = ''
}

</script>

<template>
	<form @submit.prevent="addItem">
		<input type="text" v-model='inputText' />
		<button type="submit">Add Todo</button>
	</form>
	<ul>
		<li v-for="todo in todos" :key="todo.id">{{todo.text}}</li>
	</ul>
</template>

<style scoped>

</style>
