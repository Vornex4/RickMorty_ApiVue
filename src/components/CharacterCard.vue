<template>
  <div class="card h-100 shadow-sm">
    <img :src="character.image" class="card-img-top" :alt="character.name" />
    <div class="card-body">
      <h5 class="card-title">{{ character.name }}</h5>
      <p class="card-text mb-1"><b>Status:</b> {{ character.status }}</p>
      <p class="card-text mb-1"><b>Species:</b> {{ character.species }}</p>
      <p class="card-text mb-0"><b>Origin:</b> {{ character.origin?.name }}</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  character: {
    type: Object,
    required: true
  }
})
</script>
