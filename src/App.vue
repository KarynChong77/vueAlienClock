<script setup>
  import Clock from './components/Clock.vue';
  import AlienClock from './components/AlienClock.vue';
  import {ref} from 'vue';
  
  const humanMsg = ref(null);
  const alienMsg = ref(null);

</script>

<template>
  <Clock @humanTime="(msg) => humanMsg = msg" :sendAlienTime='alienMsg'/>
  <AlienClock @alienTime="(msg) => alienMsg = msg" :sendTime='humanMsg'/>
</template>

<style scoped>

</style>
