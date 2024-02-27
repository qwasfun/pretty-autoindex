<script setup>
import { getCurrentInstance, computed } from 'vue'
defineProps(['name', 'path', 'icon', 'mtime'])

const { proxy } = getCurrentInstance()

const visibilityOptions = computed(() => {
  return proxy.$conf.visibilityOptions
})
</script>

<template>
  <li class="files__directory menu-item columns">
    <div class="flex-table-item flex-table-item-primary">
      <div class="files__icon-container">
        <span v-if="icon" class="octicon" :class="'octicon-' + icon"></span>
      </div>
      <RouterLink :to="path"> {{ name }}</RouterLink>
    </div>
    <div v-if="visibilityOptions.date.use" class="flex-table-item pr-1 text-right">
      {{ mtime }}
    </div>
  </li>
</template>
