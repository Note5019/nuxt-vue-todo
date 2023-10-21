<template>
  <div>
    Hello
    {{  appConfig }}
  </div>
</template>

<script setup lang="ts">
const appConfig = useAppConfig()
</script>