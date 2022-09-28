<script setup lang="ts">
	import { ref } from 'vue'
	const inputText = ref('')
	const pr = () => {
		console.log(inputText.value)
		inputText.value = ''
	}
</script>

<template>
  <form @submit.prevent="pr">
    <input type="text" v-model='inputText' />
	<button type="submit">Add Todo</button>
  </form>
</template>

<style scoped>

</style>
