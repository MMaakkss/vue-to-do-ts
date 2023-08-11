<script setup lang="ts">
import { IToDo } from "../types/toDo.ts";

import { ref, nextTick, computed } from "vue";

const props = defineProps<{
	toDo: IToDo,
	id: number,
}>();

const emit = defineEmits<{
	(e: "changeStatus", id: number, status: boolean): void,
	(e: "updateToDo", id: number, value: string): void,
	(e: "deleteToDo", id: number): void,
}>();

const input = ref<HTMLInputElement | null>(null);
let inputValue = ref<string>(props.toDo.value);
const isDisabled = ref<boolean>(true);

//відмічаємо задачу
const checkToDo = (): void => {
	emit("changeStatus", props.id, props.toDo.checked);
};

//редагування
const editToDo = async (): Promise<void> => {
	isDisabled.value = false;

	//чекаємо поки інпут стане актвним
	await nextTick();

	if (input.value) input.value.focus();
};

//редагування тексту
const saveChanges = (): void => {
	if (!inputValue.value.length) return;

	isDisabled.value = true;

	emit("updateToDo", props.id, inputValue.value);
};

//видалення задачі
const deleteToDo = (): void => {
	emit("deleteToDo", props.id);
};

//виводимо дату згідно локалі
const getDate = computed((): string => {
	return  new Date(props.toDo.date).toLocaleTimeString([], {year: "numeric", month: "numeric", day: "numeric", hour: "2-digit", minute: "2-digit"});
});
</script>

<template>
	<div class="todo-container">
		<div class="item">
			<div>
				<input type="checkbox" :checked="toDo.checked" @change="checkToDo">
				<input
					class="item__input"
					type="text"
					ref="input"
					:disabled="isDisabled"
					@keydown.enter="saveChanges"
					v-model="inputValue"
				>
			</div>

			<div class="item__buttons">

<!--				кнока редагування-->
				<button
					class="item__button"
					@click="editToDo"
					v-if="isDisabled"
				>
					Edit
				</button>

<!--				кнопка збереження змін-->
				<button
					class="item__button save"
					@click="saveChanges"
					v-else
				>
					Save Changes
				</button>

				<button class="item__button delete" @click="deleteToDo">Delete</button>
			</div>
		</div>
		<div class="date">Created Time: <span>{{ getDate }}</span></div>
	</div>
</template>

<style lang="scss" scoped>
.todo-container {
	margin-bottom: 24px;

	&:last-child {
		margin-bottom: 0;
	}
}

.item {
	display: flex;
	justify-content: space-between;
	gap: 20px;

	&__input {
		width: 320px;
		font-size: 16px;
		padding: 4px;
		border: none;
		background: transparent;
		border-bottom: 2px solid transparent;
		text-overflow: ellipsis;

		&:focus {
			outline: none;
			border-bottom: 2px solid #646cff;
		}
	}

	&__buttons {
		display: flex;
		gap: 8px;
	}

	&__button {
		padding: 4px 16px;

		&.delete {
			background-color: #f34747;
		}

		&.save {
			background-color: #32a921;
		}

		&.delete, &.save {
			color: #ffffff;

			&:hover {
				border-color: transparent;
			}
		}
	}
}

.date {
	font-size: 14px;
	text-align: left;

	span {
		font-style: italic;
	}
}
</style>
