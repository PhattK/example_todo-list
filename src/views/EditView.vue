<script setup>
import { ref, onMounted } from 'vue'
import { useTodoStore } from '../stores/todo'
import { useRouter, useRoute, RouterLink} from 'vue-router'

import Loading from '../components/Loading.vue'

const todoStore = useTodoStore()
const route = useRoute()
const router = useRouter()
const todoId = ref(-1)
const isLoading = ref(false)
const isLoaded = ref(false)
const isUpdated = ref(false)


onMounted(async () => {
    try {
        isLoading.value = true
        todoId.value = parseInt(route.params.id)
        await todoStore.loadTodo(todoId.value)
        isLoaded.value = true
        isLoading.value = false
    } catch (error) {
        console.log('error', error)
    }
})

const editTodo = async(selectedTodo) => {
    try {
        isLoading.value = true
        const bodyData = {
            name: selectedTodo.name,
            status: selectedTodo.status
        }
        await todoStore.editTodo(bodyData, todoId.value)
        isUpdated.value = true
        isLoading.value = false
        setTimeout(() => {
            isUpdated.value = false
        }, 2000)
        //router.push({name: 'todo-list'})
    } catch (error) {
        console.log('error', error)
    }
}
</script>
<template>
    <div class="w1/2 mx-auto">
        <div class="flex items-center mb-2">
            <RouterLink :to="{name:'todo-list'}">
                <svg class="fill-[oklch(var(--p))]" xmlns="http://www.w3.org/2000/svg" height="1.5em" viewBox="0 0 512 512"><!--!Font Awesome Free 6.6.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free Copyright 2024 Fonticons, Inc. --><path d="M512 256A256 256 0 1 0 0 256a256 256 0 1 0 512 0zM116.7 244.7l112-112c4.6-4.6 11.5-5.9 17.4-3.5s9.9 8.3 9.9 14.8l0 64 96 0c17.7 0 32 14.3 32 32l0 32c0 17.7-14.3 32-32 32l-96 0 0 64c0 6.5-3.9 12.3-9.9 14.8s-12.9 1.1-17.4-3.5l-112-112c-6.2-6.2-6.2-16.4 0-22.6z"/></svg>
            </RouterLink>
            <div class="text-xl font-semibold ml-2">
                Edit Task 
            </div>
        </div>
        <Loading v-if="isLoading"></Loading>
        <div v-if="isLoaded">
            ID : <div class="badge badge-primary text-white">{{ todoId }}</div>
        </div>
        <label class="form-control w-full">
            <div class="label">
                <span class="label-text">Name</span>
            </div>
            <input v-model="todoStore.selectedTodo.name" type="text" placeholder="List Name" class="input input-bordered w-full"  />
        </label>
        <label class="form-control w-full">
            <div class="label">
                <span class="label-text">Status</span>
            </div>
            <select class="select select-bordered text-base" v-model="todoStore.selectedTodo.status">
                <option v-for="status in todoStore.statuses" :value="status" :key="status">
                    {{ status }}
                </option>
            </select>
        </label>
        <div>
            <button class="btn btn-primary w-full mt-4 text-white" @click="editTodo(todoStore.selectedTodo)">Update</button>
        </div>
        <div class="toast toast-center toast-middle">
            <div v-if="isUpdated" class="alert alert-success">
                <span>Update Successful</span>
            </div>
        </div>
    </div>
</template>
