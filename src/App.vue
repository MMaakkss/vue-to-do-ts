<script setup lang="ts">
import ToDoItem from "./components/ToDoItem.vue";

import { IToDo } from "./types/toDo.ts";

import { ref } from "vue";

let toDoList = ref<IToDo[]>(JSON.parse(localStorage.getItem("toDoList")!) ?? []);
let inputValue = ref<string>("");

// додаємо задачу
const addToDo = (): void => {
	if (!inputValue.value.length) return;

	const newToDo: IToDo = {
		value: inputValue.value,
		checked: false,
		date: Date.now(),
	};

	toDoList.value.unshift(newToDo);
	storeData();

	inputValue.value = "";
};

// змінюємо статус задачі
const changeToDoStatus = (id: number, status: boolean): void => {
	toDoList.value.map((elem, idx) => {
		if (idx === id) elem.checked = !status;
		return elem;
	});

	storeData();
};

// редагування
const updateToDo = (id: number, value: string): void => {
	toDoList.value.map((elem, idx) => {
		if (idx === id) elem.value = value;
		console.log(elem)
		return elem;
	});

	storeData();
};

// видалення
const deleteToDo = (id: number): void => {
	toDoList.value = toDoList.value.filter((elem, idx) => idx !== id);

	storeData();
};

// зберігаємо данні
const storeData = (): void => {
	localStorage.setItem("toDoList", JSON.stringify(toDoList.value));
};
</script>

<template>
	<div class="container">

		<form class="form">
			<input
				class="form__input"
				type="text"
				v-model="inputValue"
				@keydown.enter="addToDo"
			/>
			<button class="form__button" @click.prevent="addToDo">Add ToDo</button>
		</form>

		<div class="list">
			<ToDoItem
				v-for="(toDo, idx) in toDoList"
				:key="toDo.date"
				:id="idx"
				:to-do="toDo"
				@change-status="changeToDoStatus"
				@update-to-do="updateToDo"
				@delete-to-do="deleteToDo"
			/>
		</div>

	</div>
</template>

<style lang="scss" scoped>
.container {
	max-width: 600px;
	margin: auto;
}

.form {
	display: flex;
	justify-content: center;
	gap: 20px;

	&__input {
		flex: 1;
		font-size: 16px;
		padding: 8px 16px;
		border-radius: 8px;
		border: 1px solid #dfdfdf;

		&:focus {
			outline: none;
		}
	}
}

.list {
	margin-top: 40px;
}
</style>
