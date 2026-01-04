<script setup>
import BaseCard from '@/components/BaseCard.vue'
import axios from 'axios'
import { onMounted, ref } from 'vue'

const episodes = ref([])

const page = ref(1)
const totalPages = ref(0)

onMounted(() => {
  handleGetEpisodes()
})

async function handleGetEpisodes() {
  try {
    const response = await axios.get('https://rickandmortyapi.com/api/episode', {
      params: {
        page: page.value,
        totalPages: response.data.info.pages,
      },
    })

    episodes.value = response.data.results

    console.log(response)
  } catch (error) {
    console.log(error)
  }
}

function setPagination(newPage) {
  page.value = newPage

  handleGetEpisodes()
}
</script>

<template>
  <div class="p-4">
    <h1>Episodes</h1>

    <div class="grid grid-cols-1 md:grid-cols-3 mg:grid-cols-3 gap-6">
      <BaseCard
        v-for="episode in episodes"
        :key="episode.id"
        :name="episode.name"
        :episode="episode.episode"
        :characters="episode.characters"
        :airDate="episode.air_date"
      />

      <div class="flex justify-center py-6">
        <div class="join">
          <button
            v-for="(currentPage, index) in totalPages"
            :key="index"
            class="join-item btn btn-lg md:btn-md"
            :class="{ 'btn-active': index + 1 === currentPage }"
            @click="setPagination(index + 1)"
          >
            {{ index + 1 }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
