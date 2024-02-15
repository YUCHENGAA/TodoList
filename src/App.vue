<script setup>
import TodoList from './components/TodoList.vue'
</script>

<template>
  <h1>代辦清單</h1>
  <TodoList />
</template>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: Arial, "PingFang SC", "Microsoft Yahei", sans-serif;
}

body {
  background-color: #c5d5f0;
  opacity: 1;
  background-size: 12px 12px;
  color: rgb(16, 16, 16);
}

#app {
  width: 100vw;
  height: 100vh;
  max-width: 100%;
  place-items: center;
}
</style>