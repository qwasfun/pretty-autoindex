<script setup>
import Loading from '../components/Loading.vue'
import Directory from '../components/Directory.vue'
import File from '../components/File.vue'
import { RouterLink } from 'vue-router'

import packageJson from '../../package.json'

import { useRouter, useRoute } from 'vue-router'
import { ref, onMounted, computed, getCurrentInstance } from 'vue'

const router = useRouter()
const route = useRoute()

let path = ref('')
let files = ref(null)
let loading = ref(false)
let failed = ref(false)

const pathArray = computed(() => {
  return path.value.split('/').filter((e) => e !== '')
})

const { proxy } = getCurrentInstance()

onMounted(() => {
  const dir = route.path
  fetchFileInfo(dir)
})

router.afterEach((to) => {
  fetchFileInfo(to.fullPath)
})

function fetchFileInfo(dir) {
  path.value = dir === '/' ? '' : dir

  failed.value = false
  loading.value = true
  const xhr = new XMLHttpRequest()
  const address = proxy.$conf.address + dir
  xhr.open('GET', address)
  xhr.onloadend = () => {
    if (xhr.status !== 200) {
      failed.value = true
      loading.value = false
    } else {
      try {
        files.value = JSON.parse(xhr.responseText)

        loading.value = false
      } catch (e) {
        failed.value = true
        loading.value = false
      }
    }
  }
  xhr.send()
}
</script>

<template>
  <Loading :loading="loading" :failed="failed"></Loading>
  <div class="container">
    <div class="app">
      <div class="breadcrumb">
        <span class="breadcrumb__root">
          <RouterLink to="/"> {{ $conf.name }}</RouterLink>
        </span>
        <span class="breadcrumb__separator">/</span>
        <template v-for="(dir, i) in pathArray" :key="dir">
          <span class="breadcrumb__directory" :class="{ 'last-one': pathArray.length === i + 1 }">
            <RouterLink :to="'/' + pathArray.slice(0, i + 1).join('/') + '/'">
              {{ decodeURIComponent(dir) }}
            </RouterLink>
          </span>
          <span class="breadcrumb__separator">/</span>
        </template>
      </div>
      <ul v-if="files !== null" class="files menu">
        <template v-if="pathArray.length > 0">
          <Directory name=".." :path="pathArray.slice(0, -1).join('/') + '/'"> </Directory>
        </template>
        <template v-for="file in files">
          <Directory
            :key="path + file.name"
            v-if="file.type === 'directory'"
            :icon="'file-directory'"
            :name="file.name"
            :path="path + file.name + '/'"
            :mtime="file.mtime"
          >
          </Directory>

          <File
            v-if="file.type === 'file'"
            icon="file-text"
            :key="path + file.name"
            :path="path + file.name"
            :name="file.name"
            :mtime="file.mtime"
            :size="file.size"
          >
          </File>
        </template>
      </ul>
    </div>
  </div>

  <footer class="footer">
    <div class="footer__text">
      Generated with
      <a href="{{ packageJson.homepage }}" class="muted-link">
        {{ packageJson.name }}
      </a>
      {{ packageJson.version }}.
    </div>
    <div class="footer__mark">
      <a href="{{ packageJson.homepage }}" class="muted-link">
        <span class="octicon octicon-mark-github"></span>
      </a>
    </div>
  </footer>
</template>
