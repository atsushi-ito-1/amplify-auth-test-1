<script setup>
import { Authenticator } from '@aws-amplify/ui-vue'
import '@aws-amplify/ui-vue/styles.css'
</script>

<template>
  <authenticator>
    <template v-slot="{ user, signOut }">
      <h1>Hello {{ user.username }}!</h1>
      <button @click="signOut">Sign Out</button>
      <div id="app">
        <TodoTest />
      </div>
    </template>
  </authenticator>
</template>

<script>
import TodoTest from "./components/TodoTest.vue"
export default {
  name: 'app',
  components: {
    TodoTest
  }
}
</script>