<script setup>
import BaseCard from '@/components/BaseCard.vue'
import axios from 'axios'
import { onMounted, ref } from 'vue'

const episodes = ref([])

onMounted(() => {
  handleGetEpisodes()
})

async function handleGetEpisodes() {
  try {
    const response = await axios.get('https://rickandmortyapi.com/api/episode')

    episodes.value = response.data.results

    console.log(response)
  } catch (error) {
    console.log(error)
  }
}
</script>

<template>
  <div class="p-4">
    Home
    <div class="grid grid-cols-1 md:grid-cols-3 mg:grid-cols-3 gap-6">
      <BaseCard
        v-for="episode in episodes"
        :key="episode.id"
        :name="episode.name"
        :episode="episode.episode"
        :characters="episode.characters"
        :airDate="episode.air_date"
      />

      {{ episodes }}
    </div>
  </div>
</template>
