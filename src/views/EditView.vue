<script setup>
import { ref, onMounted } from 'vue'
import { useTodoStore } from '../stores/todo'
import { useRouter, useRoute, RouterLink} from 'vue-router'

const todoStore = useTodoStore()
const route = useRoute()
const router = useRouter()
const todoId = ref(-1)
const isLoading = ref(false)


onMounted(async () => {
    try {
        todoId.value = parseInt(route.params.id)
        await todoStore.loadTodo(todoId.value)
        isLoading.value = true
    } catch (error) {
        console.log('error', error)
    }
})

const editTodo = async(selectedTodo) => {
    try {
        const bodyData = {
            name: selectedTodo.name,
            status: selectedTodo.status
        }
        await todoStore.editTodo(bodyData, todoId.value)
        alert('Complete!!')
        router.push({name: 'todo-list'})
    } catch (error) {
        console.log('error', error)
    }
}
</script>
<template>
    <div>
        Edit Task 
        <RouterLink :to="{name:'todo-list'}">Back</RouterLink>
        <div v-if="isLoading">
            ID : {{ todoId }}
        </div>
        <div v-else="">
            <h3>Loading...</h3>
        </div>
    </div>
    <div>
        <div>Name : </div>
        <input type="text" v-model="todoStore.selectedTodo.name">
        <div>Status : </div>
        <select v-model="todoStore.selectedTodo.status">
            <option v-for="status in todoStore.statuses" :value="status">
                {{ status }}
            </option>
        </select>
        <button @click="editTodo(todoStore.selectedTodo)">Update</button>
    </div>
</template>
