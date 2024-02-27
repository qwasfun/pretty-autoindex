<script setup>
import { getCurrentInstance } from 'vue'
const { proxy } = getCurrentInstance()

const props = defineProps(['name', 'path', 'icon', 'mtime', 'size'])

const visibilityOptions = proxy.$conf.visibilityOptions
const link = proxy.$conf.address + props.path
</script>

<template>
  <li class="files__file menu-item flex-table">
    <div class="flex-table-item flex-table-item-primary">
      <div class="files__icon-container">
        <span v-if="icon" class="octicon" :class="'octicon-' + icon"></span>
      </div>
      <a :href="link">{{ name }}</a>
    </div>
    <div v-if="visibilityOptions.size.use" class="flex-table-item pr-1 text-right">
      {{ size }}
    </div>
    <div v-if="visibilityOptions.date.use" class="flex-table-item pr-1 text-right">
      {{ mtime }}
    </div>
  </li>
</template>
