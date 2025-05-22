<script setup lang="ts">
import Blockchain from './components/Blockchain.vue';
import Registry from './components/Registry.vue';


</script>

<template>
  <div class="min-h-screen flex flex-col items-center text-white px-4">

    <Registry></Registry>

    <Blockchain></Blockchain>

  </div>

</template>



<style scoped>
body {
  font-family: 'Inter', sans-serif;
}
</style>
