<script setup>
import { ref, reactive } from 'vue'
import { API, graphqlOperation } from 'aws-amplify'
import * as Mutation from '../graphql/mutations'
import * as Query from '../graphql/queries'
// import * as Subscription from '../graphql/subscriptions'
const name = ref('')
const description = ref('')
const priority = ref(3)
const todos = reactive([])
const note = reactive({}) // for debugging.
getTodos()
// subscribe()

async function getTodos() {
//    todos.value = (await API.graphql(graphqlOperation(Query.listTodos))).data.listTodos.items
    searchTodos()
}

async function searchTodos() {
    const response = await API.graphql(graphqlOperation(Query.searchTodos, {
        limit: 100,
        sort: { direction: "desc", field: "created" },
    }))
    todos.value = response.data.searchTodos.items
}

async function createTodo() {
    if (!name.value || !description.value) return;
    const created = Math.ceil(Date.now() / 1000)
    const updated = created
    const todo = { name: name.value, description: description.value, priority: priority.value, created, updated}
    name.value = ''
    description.value = ''
    const response = await API.graphql(graphqlOperation(Mutation.createTodo, { input: todo }))
    note.value = response
    todos.value.unshift(response.data.createTodo)
}

async function deleteTodo(id) {
    await API.graphql(graphqlOperation(Mutation.deleteTodo, { input: { id } }))
    todos.value = todos.value.filter((todo) => todo.id !== id)
}

// function subscribe() {
//     API.graphql(graphqlOperation(Subscription.onCreateTodo))
//         .subscribe({
//             next: (eventData) => {
//                 let todo = eventData.value.data.onCreateTodo
//                 if (todos.value.some(item => item.name === todo.name)) return
//                 getTodos()
//             }
//         })
//     API.graphql(graphqlOperation(Subscription.onDeleteTodo))
//         .subscribe({
//             next: () => {
//                 getTodos()
//             }
//         })
// }

</script>

<template>
    <div class="todos">
        <h1>Todo App</h1>
        <input type="text" v-model="name" placeholder="Todo name" />
        <input type="text" v-model="description" placeholder="Todo description" />
        <input type="text" v-model="priority" placeholder="Todo priority" />
        <button @click="createTodo">Create Todo</button>
        <p>{{ note.value }}</p>
        <div v-for="todo in todos.value" :key="todo.id">
            <h3>{{ todo.name }}</h3>
            <p>
                {{ new Date(Date.parse(todo.createdAt)).toLocaleString() }}
                {{ todo.description }}
                優先度:{{ todo.priority }}
                <span
                    @click="deleteTodo(todo.id)"
                >[x]</span>
            </p>
        </div>
    </div>
</template>
