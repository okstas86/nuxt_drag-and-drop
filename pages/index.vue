<template>
	<div class="w-full h-full flex p-5 bg-gray-100 overflow-x-auto">
		<div
			v-for="category in categories"
			:key="category.id"
			@drop="onDrop($event, category.id)"
			@dragover.prevent
			@dragenter.prevent
			class="droppable min-w-[250px] h-full m-5 p-5 border bg-gray-500 rounded-2xl flex flex-col"
		>
			<h4
				@click="startEditing(category)"
				v-if="!category.editing"
				class="text-white mb-3 text-center"
			>
				{{ category.title }}
			</h4>
			<input
				class="bg-gray-500 text-white mb-3 outline-none text-center"
				v-if="category.editing"
				v-model="category.editedTitle"
				@blur="stopEditing(category)"
				@keyup.enter="stopEditing(category)"
				type="text"
			/>
			<div
				v-for="item in items.filter(x => x.categoryId === category.id)"
				:key="item.id"
				@dragstart="onDragStart($event, item)"
				draggable="true"
				class="draggable bg-gray-800 text-white p-2 rounded-md mb-2"
			>
				<h5 @click="startEditing(item)" v-if="!item.editing">
					{{ item.title }}
				</h5>
				<input
					class="bg-gray-800"
					v-if="item.editing"
					v-model="item.editedTitle"
					@blur="stopEditing(item)"
					@keyup.enter="stopEditing(item)"
					type="text"
				/>
			</div>
			<button
				@click="addNewItem(category.id)"
				class="m-2 bg-green-500 text-white p-2 rounded-md"
			>
				Add Item
			</button>
		</div>
		<div>
			<button
				@click="addNewCategory"
				class="m-5 bg-gray-500 text-white p-2 rounded-md min-w-[200px]"
			>
				Add another column
			</button>
		</div>
	</div>
</template>

<script setup>
import { ref } from "vue"
const items = ref([
	{
		id: 0,
		title: "Audi",
		categoryId: 0,
		editing: false,
	},
	{
		id: 1,
		title: "Mersedes",
		categoryId: 0,
		editing: false,
	},
	{
		id: 2,
		title: "Cat",
		categoryId: 1,
		editing: false,
	},
])
const categories = ref([
	{
		id: 0,
		title: "Cars",
		editing: false,
	},
	{
		id: 1,
		title: "Animals",
		editing: false,
	},
])

let newItemTitle = ""

function onDragStart(e, item) {
	e.dataTransfer.dropEffect = "move"
	e.dataTransfer.effectAllowed = "move"
	e.dataTransfer.setData("itemId", item.id.toString())
}
function onDrop(e, categoryId) {
	const itemId = parseInt(e.dataTransfer.getData("itemId"))
	items.value = items.value.map(item => {
		if (item.id === itemId) item.categoryId = categoryId
		return item
	})
}
function addNewCategory() {
	const newCategoryId = categories.value.length
	const newCategory = {
		id: newCategoryId,
		title: `New Category`,
	}
	categories.value = [...categories.value, newCategory]
}

function addNewItem(categoryId) {
	const newItemId = items.value.length
	const newItem = {
		id: newItemId,
		title: `New Item`,
		categoryId: categoryId,
		editing: false,
		editedTitle: "",
	}
	items.value = [...items.value, newItem]
	newItemTitle = ""
}

function startEditing(item) {
	item.editing = true
	item.editedTitle = item.title
}

function stopEditing(item) {
	item.editing = false
	item.title = item.editedTitle
}
</script>
