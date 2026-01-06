<script setup>
import BaseCard from '@/components/BaseCard.vue'
import ModalEpisode from '@/components/ModalEpisode.vue'
import axios from 'axios'
import { onMounted, ref } from 'vue'

const episodes = ref([])

const page = ref(1)
const totalPages = ref(0)
const episodeSelected = ref('')
const showModal = ref(false)

onMounted(() => {
  handleGetEpisodes()
})

async function handleGetEpisodes() {
  try {
    const response = await axios.get('https://rickandmortyapi.com/api/episode', {
      params: {
        page: page.value,
      },
    })
    console.log(response.data.info.pages)

    episodes.value = response.data.results
    totalPages.value = response.data.info.pages
  } catch (error) {
    console.log(error)
  }
}

function setPagination(newPage) {
  page.value = newPage

  handleGetEpisodes()
}

function openModal(episodeId) {
  episodeSelected.value = episodeId.toString()
  showModal.value = true
}
</script>

<template>
  <div class="p-4 bg-base-200">
    <div class="grid grid-cols-1 md:grid-cols-3 mg:grid-cols-3 gap-6">
      <BaseCard
        v-for="episode in episodes"
        :key="episode.id"
        :title="episode.name"
        :episode="episode.episode"
        :characters="episode.characters"
        :air-date="episode.air_date"
        @clickEpisode="openModal(episode.id)"
      />
    </div>

    <div class="flex justify-center py-6">
      <div class="join">
        <button
          v-for="(currentPage, index) in totalPages"
          :key="index"
          class="join-item btn btn-lg md:btn-md"
          :class="{ 'btn-active': page === currentPage }"
          @click="setPagination(index + 1)"
        >
          {{ currentPage }}
        </button>
      </div>
    </div>

    <ModalEpisode :id="episodeSelected" v-model="showModal" />
  </div>
</template>
