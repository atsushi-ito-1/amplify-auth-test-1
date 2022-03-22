<template>
    <div class="todos">
        <h1>Todo App</h1>
        <input type="text" v-model="name" placeholder="Todo name" />
        <input type="text" v-model="description" placeholder="Todo description" />
        <button @click="createTodo">Create Todo</button>
        <div v-for="todo in todos" :key="todo.id">
            <h3>{{ todo.name }}</h3>
            <p>
                {{ todo.description }}
                <span @click="deleteTodo(todo.id)">[x]</span>
            </p>
        </div>
    </div>
</template>

<script>
import { API } from 'aws-amplify'
import { createTodo, deleteTodo } from '../graphql/mutations'
import { listTodos } from '../graphql/queries'
import { onCreateTodo } from '../graphql/subscriptions'

export default {
    name: 'TodoTest',
    async created() {
        this.getTodos()
        this.subscribe()
    },
    data() {
        return {
            name: '',
            description: '',
            todos: []
        }
    },
    methods: {
        async createTodo() {
            const { name, description } = this
            if (!name || !description) return
            const todo = { name, description }
            this.todos = [...this.todos, todo]
            await API.graphql({ query: createTodo, variables: { input: todo } });
            this.name = ''
            this.description = ''
        },
        async deleteTodo(id) {
            await API.graphql({ query: deleteTodo, variables: { input: { id } } })
            this.getTodos()
        },
        async getTodos() {
            const todos = await API.graphql({ query: listTodos })
            this.todos = todos.data.listTodos.items
        },
        subscribe() {
            API.graphql({ query: onCreateTodo })
                .subscribe({
                    next: (eventData) => {
                        let todo = eventData.value.data.onCreateTodo
                        if (this.todos.some(item => item.name === todo.name)) return // remove duplications
                        this.todos = [...this.todos, todo]
                    }
                })
        }
    }
}
</script>