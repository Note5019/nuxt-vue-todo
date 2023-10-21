<template>
  <UContainer>
    <UForm class="flex flex-col gap-2" :validate="validate" :state="state" @submit="submit">
      <UFormGroup label="Task" name="task">
        <UInput v-model="state.task" />
      </UFormGroup>

      <UButton type="submit">
        Add
      </UButton>
    </UForm>

    <UTable :rows="todos" :columns="columns" :loading="isLoading">
      <template #actions1-data="{ row }">
        <UButton color="gray" icon="i-heroicons-bolt-20-solid" @click="doneTodo(row.id)" />
      </template>
      <template #task-data="{ row }">
        <span :class="[row.status === 'Done' && 'line-through text-primary-500 dark:text-primary-400']">{{ row.task
        }}</span>
      </template>
      <template #actions2-data="{ row }">
        <UButton color="gray" icon="i-heroicons-trash-20-solid" @click="deleteTodo(row.id)" />
      </template>
    </UTable>
  </UContainer>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import type { FormError, FormSubmitEvent } from '@nuxt/ui/dist/runtime/types'

const BASE_URL = 'https://6533ed4fe1b6f4c59046646a.mockapi.io';
const statuses = ['Pending', 'Done'];
const todos = ref([]);
const state = ref({
  task: undefined,
});
const isLoading = ref(true);
const columns = [
  {
    key: 'actions1'
  },
  // {
  //   key: 'id',
  //   label: 'ID'
  // },
  {
    key: 'task',
    label: 'Task'
  },
  {
    key: 'status',
    label: 'Status'
  },
  {
    key: 'actions2'
  }
]

const loadTodos = async () => {
  isLoading.value = true;
  const { data } = await useFetch(`${BASE_URL}/api/v1/todos`, {
    lazy: true
  });
  todos.value = data.value;
  isLoading.value = false;
}
const addTodo = async (task) => {
  try {
    isLoading.value = true;
    await useFetch(`${BASE_URL}/api/v1/todos`, {
      method: 'POST',
      body: {
        task: task,
        status: statuses[0],
      }
    });
    isLoading.value = false;
  } catch (error) {
    console.log('error', error);
    isLoading.value = false;
  }
}
const deleteTodo = async (id) => {
  try {
    isLoading.value = true;
    await useFetch(`${BASE_URL}/api/v1/todos/${id}`, {
      method: 'DELETE'
    });
    await loadTodos();
    isLoading.value = false;
  } catch (error) {
    console.log('error', error);
    isLoading.value = false;
  }
}
const doneTodo = async (id) => {
  try {
    isLoading.value = true;
    await useFetch(`${BASE_URL}/api/v1/todos/${id}`, {
      method: 'PUT',
      body: {
        status: statuses[1],
      }
    });
    await loadTodos();
    isLoading.value = false;
  } catch (error) {
    console.log('error', error);
    isLoading.value = false;
  }
}

const validate = (state: any): FormError[] => {
  const errors: Array<any> = []
  if (!state.task) errors.push({ path: 'task', message: 'Required' })
  return errors
}

async function submit(event: FormSubmitEvent<any>) {
  let data = event.data;
  await addTodo(data.task);
  await loadTodos();
}

loadTodos()
</script>

