<template>
  <div>
    Hello
    {{ appConfig }}
    <div v-for="todo in todos">
      <span>{{ todo.task }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
const appConfig = useAppConfig()
const { data: todos } = await useFetch('https://6533ed4fe1b6f4c59046646a.mockapi.io/api/v1/todos')
console.log(todos.value);
</script>