<script setup lang="ts">
import Todo from "./components/Todo.vue";
</script>

<template>
  <a-config-provider
      :theme="{
      token: {
        colorPrimary: '#00b96b',
      },
    }"
  >
    <todo></todo>
  </a-config-provider>
</template>

<style>

</style>
