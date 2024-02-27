<script setup>
defineProps(['loading', 'failed'])
</script>

<template>
  <div v-if="loading || failed" class="loading">
    <div class="container">
      <div v-if="loading" class="loading__spinner"></div>
      <div v-if="failed" class="loading__failed">Load failed.</div>
    </div>
  </div>
</template>
