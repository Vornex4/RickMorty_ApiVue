<template>
  <div class="container py-4">
    <div class="d-flex align-items-center justify-content-between mb-3">
      <h1 class="h3 m-0">Rick & Morty</h1>

      <div class="d-flex gap-2">
        <button class="btn btn-outline-secondary" :disabled="!prevUrl || loading" @click="go(prevUrl)">
          Back
        </button>
        <button class="btn btn-outline-secondary" :disabled="!nextUrl || loading" @click="go(nextUrl)">
          Next
        </button>
      </div>
    </div>

    <div class="row g-3 mb-3">
      <div class="col-12 col-md-6">
        <input
          v-model="search"
          class="form-control"
          placeholder="Buscar por nombre"
          @keyup.enter="searchByName"
        />
      </div>
      <div class="col-12 col-md-auto">
        <button class="btn btn-primary w-100" :disabled="loading" @click="searchByName">
          Buscar
        </button>
      </div>
      <div class="col-12 col-md-auto">
        <button class="btn btn-outline-dark w-100" :disabled="loading" @click="reset">
          Reset
        </button>
      </div>
    </div>

    <div v-if="error" class="alert alert-danger">{{ error }}</div>
    <div v-if="loading" class="alert alert-info">Cargando...</div>

    <div class="row g-3">
      <div v-for="ch in characters" :key="ch.id" class="col-12 col-sm-6 col-md-4 col-lg-3">
        <CharacterCard :character="ch" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import CharacterCard from './components/CharacterCard.vue'

const characters = ref([])
const loading = ref(false)
const error = ref('')
const nextUrl = ref(null)
const prevUrl = ref(null)
const search = ref('')

async function fetchCharacters(url = 'https://rickandmortyapi.com/api/character') {
  loading.value = true
  error.value = ''
  try {
    const res = await fetch(url)
    if (!res.ok) {
      // 404 cuando no hay resultados en búsqueda
      if (res.status === 404) throw new Error('No se encontraron personajes.')
      throw new Error(`HTTP ${res.status}`)
    }
    const data = await res.json()
    characters.value = data.results ?? []
    nextUrl.value = data.info?.next ?? null
    prevUrl.value = data.info?.prev ?? null
  } catch (e) {
    error.value = e.message || 'Error cargando personajes.'
    characters.value = []
    nextUrl.value = null
    prevUrl.value = null
  } finally {
    loading.value = false
  }
}

function go(url) {
  fetchCharacters(url)
}

function searchByName() {
  const name = search.value.trim()
  if (!name) return fetchCharacters()
  fetchCharacters(`https://rickandmortyapi.com/api/character?name=${encodeURIComponent(name)}`)
}

function reset() {
  search.value = ''
  fetchCharacters()
}

onMounted(() => fetchCharacters())
</script>
