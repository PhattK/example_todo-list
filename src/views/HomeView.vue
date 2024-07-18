<script setup>
import { ref, onMounted, computed } from 'vue';
import { RouterLink} from 'vue-router'
import { useTodoStore } from '../stores/todo'

import Loading from '../components/Loading.vue'

const todoStore = useTodoStore()
const todoText = ref('')
const isLoading = ref(false)
const selectedStatus = ref('Pending')

const filterTodoList = computed (() => {
    return todoStore.list.filter(todo => todo.status === selectedStatus.value)
})

onMounted(async () => {
    isLoading.value = true
    await todoStore.loadTodos()
    isLoading.value = false
})

const addTodo = async(todoText) => {
    isLoading.value = true
    try {
        await todoStore.addTodo(todoText)
        await todoStore.loadTodos()
    } catch (error) {
        console.log('error', error)
    }
    isLoading.value = false
}

const updateStatus = async(todoId, todoStatus) => {
    isLoading.value = true
    try {
        await todoStore.editTodo({
            status: todoStatus
        }, todoId)
    } catch (error) {
        console.log('error', error)
    }
    isLoading.value = false
}

const removeTodo = async (id) => {
    isLoading.value = true
    try {
        await todoStore.removeTodo(id)
        await todoStore.loadTodos()
    } catch (error) {
        console.log('error', error)
    }
    isLoading.value = false
}

const changeStatus = async(event,todoId) => {
    try {
        if (event.target.checked) {
        await todoStore.editTodo({status: 'Done'}, todoId)
    } else {
        await todoStore.editTodo({status: 'Doing'}, todoId)
    }
    await todoStore.loadTodos()
    } catch (error) {
        console.log('error', error)
    }
}

const changeSelectedStatus = async(newStatus) => {
    selectedStatus.value = newStatus
}
</script>

<template>
    <div class="text-xl font-bold mb-2">
        <h1>Todo List</h1>
    </div>
    <div class="flex mb-2">
        <input class="input input-bordered w-full" type="text" v-model="todoText" placeholder="List Name...">
        <button class="btn btn-primary text-white ml-4" @click="addTodo(todoText)">ADD</button>
    </div>
    <Loading v-if="isLoading"></Loading>
    <div role="tablist" class="tabs tabs-boxed mt-2">
        <a 
            v-for="status in todoStore.statuses" role="tab" 
            :class="status === selectedStatus ? 'tab tab-active !text-white' : 'tab'"
            @click="changeSelectedStatus(status)"
            >
            {{ status }}
        </a>
    </div>
    <div>
        <div class="flex items-center justify-between mt-2" v-for="todo in filterTodoList" :key="todo.id">
            <div class="flex">
                <input type="checkbox" :checked="todo.status === 'Done'" class="checkbox mr-2" @change="changeStatus($event, todo.id)" />
                <div :class="todo.status === 'Done' ? 'line-through' : ''"> 
                {{ todo.name }}
                </div>
            </div> 
            <div>
                <RouterLink :to="{name: 'todo-edit', params: {id: todo.id}}">
                    <button class="btn btn-square btn-secondary btn-outline group">
                        <svg class="group-hover:fill-white" xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 512 512"><!--!Font Awesome Free 6.6.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free Copyright 2024 Fonticons, Inc. --><path d="M471.6 21.7c-21.9-21.9-57.3-21.9-79.2 0L362.3 51.7l97.9 97.9 30.1-30.1c21.9-21.9 21.9-57.3 0-79.2L471.6 21.7zm-299.2 220c-6.1 6.1-10.8 13.6-13.5 21.9l-29.6 88.8c-2.9 8.6-.6 18.1 5.8 24.6s15.9 8.7 24.6 5.8l88.8-29.6c8.2-2.7 15.7-7.4 21.9-13.5L437.7 172.3 339.7 74.3 172.4 241.7zM96 64C43 64 0 107 0 160L0 416c0 53 43 96 96 96l256 0c53 0 96-43 96-96l0-96c0-17.7-14.3-32-32-32s-32 14.3-32 32l0 96c0 17.7-14.3 32-32 32L96 448c-17.7 0-32-14.3-32-32l0-256c0-17.7 14.3-32 32-32l96 0c17.7 0 32-14.3 32-32s-14.3-32-32-32L96 64z"/></svg>
                    </button>
                </RouterLink>
                <button class="btn btn-square btn-secondary btn-outline ml-2 group" @click="removeTodo(todo.id)">
                    <svg class="group-hover:fill-white" xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 448 512"><!--!Font Awesome Free 6.6.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free Copyright 2024 Fonticons, Inc. --><path d="M135.2 17.7L128 32 32 32C14.3 32 0 46.3 0 64S14.3 96 32 96l384 0c17.7 0 32-14.3 32-32s-14.3-32-32-32l-96 0-7.2-14.3C307.4 6.8 296.3 0 284.2 0L163.8 0c-12.1 0-23.2 6.8-28.6 17.7zM416 128L32 128 53.2 467c1.6 25.3 22.6 45 47.9 45l245.8 0c25.3 0 46.3-19.7 47.9-45L416 128z"/></svg>
                </button>
            </div>
        </div>
    </div>
</template>

<style scoped>
svg {
    fill: oklch(var(--p))
}
</style>

