<script setup>
import { Authenticator } from '@aws-amplify/ui-vue'
import '@aws-amplify/ui-vue/styles.css'
import TodoTest from "./components/TodoTest.vue"
</script>

<template>
  <div id="app">
    <authenticator>
      <template v-slot="{ user, signOut }">
        <h1>Hello {{ user.username }}!</h1>
        <button @click="signOut">Sign Out</button>
        <TodoTest />
      </template>
    </authenticator>
  </div>
</template>

<script>
export default {
  name: 'app',
  components: {
    TodoTest
  }
}
</script>