<script setup lang="ts">
import { ref } from 'vue';
import { TypeFlow } from '../..';
const text = ref('This is a string which has, amongst other things, punctuation... Great eh?!');
const handleUpdate = () => {
  text.value = 'Now its something else... simply incredible.';
};
</script>

<template>
  <section class="bg-white dark:bg-slate-800">
    <main class="mx-40 pt-20"></main>
    <TypeFlow
      ><p>{{ text }}</p></TypeFlow
    >
    <button @click="handleUpdate">Update Text</button>
  </section>
</template>

<style scoped></style>
