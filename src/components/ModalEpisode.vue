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
  <dialog class="modal" :class="{ 'modal-open': show }">
    <div class="modal-box">
      <button class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2" @click="closeModal">
        ✕
      </button>

      <template v-if="episode">
        <h3 class="text-xl text-center text-secondary font-semibold pb-6">
          Episode <Badge background="secondary" color="base-100">{{ episode.episode }}</Badge>
        </h3>

        <div class="flex flex-col gap-4">
          <h3 class="text-primary"><span class="font-semibold">Name: </span>{{ episode.name }}</h3>

          <div>
            <p class="text-primary pb-2 font-semibold">Characters:</p>

            <div class="avatar p-1" v-for="(character, index) in episode.characters" :key="index">
              <div class="w-16 rounded-full">
                <img :src="getImage(character)" />
              </div>
            </div>
          </div>

          <Badge outline>Air date: {{ episode.air_date }}</Badge>
        </div>
      </template>

      <div v-else class="flex justify-center">
        <span class="loading loading-dots loading-md"></span>
      </div>
    </div>

    <div class="modal-backdrop">
      <button @click="closeModal"></button>
    </div>
  </dialog>
</template>
