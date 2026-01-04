<script setup lang="ts">
import axios from 'axios'
import Badge from './Badge.vue'
import { onMounted, ref, watch } from 'vue'

const props = defineProps({
  id: String,
})

onMounted(() => {
  handleGetEpisode()
})

const episode = ref(null)

const show = defineModel()

async function handleGetEpisode() {
  try {
    const response = await axios.get(`https://rickandmortyapi.com/api/episode/${props.id}`)

    episode.value = response.data
  } catch (error) {
    console.log(error)
  }
}

watch(show, (newValue) => {
  if (newValue) {
    handleGetEpisode()
  } else {
    episode.value = null
  }
})

function getImage(url) {
  const newUrl = url.replace(
    'https://rickandmortyapi.com/api/character/',
    'https://rickandmortyapi.com/api/character/avatar/',
  )

  return `${newUrl}.jpeg`
}

function closeModal() {
  show.value = false
}
</script>

<template>
  <dialog id="modal-episode" class="modal" :class="{ 'modal-open': show }">
    <div class="modal-box" v-if="episode">
      <form method="dialog">
        <button @click="closeModal" class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">
          ✕
        </button>
      </form>

      <h3 class="text-lg font-bold mb-4">
        {{ episode.name }}

        <Badge color="white"> {{ episode.episode }} </Badge>
      </h3>

      <div class="avatar" v-for="(character, index) in episode.characters" :key="index">
        <div class="w-16 rounded-full">
          <img :src="getImage(character)" alt="character-image" />
        </div>
      </div>

      <p class="py-4">
        <Badge outline> {{ episode.air_date }} </Badge>
      </p>
    </div>

    <div class="modal-box flex justify-center" v-else>
      <span class="loading loading-dots loading-md"></span>
    </div>
  </dialog>
</template>
