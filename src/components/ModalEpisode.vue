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
  selectedCharacter.value = null
}

const selectedCharacter = ref(null)

async function handleGetCharacter(characterUrl: string) {
  const characterId = characterUrl.split('/').at(-1)

  try {
    const response = await axios.get(`https://rickandmortyapi.com/api/character/${characterId}`)

    selectedCharacter.value = response.data
  } catch (error) {
    console.log(error)
  }
}
</script>

<template>
  <dialog class="modal" :class="{ 'modal-open': show }">
    <div class="modal-box w-11/12 max-w-2xl">
      <button class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2" @click="closeModal">
        ✕
      </button>

      <div v-if="episode">
        <div class="card-body p-0">
          <h3 class="card-title justify-center text-primary">
            Episode

            <Badge>
              {{ episode.episode }}
            </Badge>
          </h3>

          <div class="card bg-base-100 shadow mb-6">
            <div class="stats stats-vertical shadow">
              <div class="stat">
                <div class="stat-title text-primary">Name</div>
                <div class="stat-value text-base text-neutral">
                  {{ episode.name }}
                </div>
              </div>

              <div class="stat">
                <div class="stat-title text-primary">Air date</div>
                <div class="stat-value text-base text-neutral">
                  {{ episode.air_date }}
                </div>
              </div>

              <div class="stat">
                <div class="stat-title text-primary">Total of characters</div>
                <div class="stat-value text-base text-neutral">
                  {{ episode.characters.length }}
                </div>
              </div>

              <div class="stat">
                <div class="stat-title text-primary">Character List</div>
                <div class="stat-value text-base text-neutral flex flex-wrap">
                  <div
                    v-for="(character, index) in episode.characters"
                    :key="index"
                    class="avatar p-1"
                  >
                    <div
                      class="w-16 rounded-full hover:cursor-pointer transition-transform duration-300 hover:scale-110"
                      @click="handleGetCharacter(character)"
                    >
                      <img :src="getImage(character)" />
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div v-if="selectedCharacter" class="card bg-base-100 shadow">
          <div class="card-body p-0">
            <h3 class="card-title justify-center text-secondary">Character Info</h3>

            <div class="flex justify-center">
              <div class="avatar">
                <div
                  class="w-28 rounded-full ring ring-secondary ring-offset-base-100 ring-offset-2"
                >
                  <img :src="getImage(selectedCharacter.url)" />
                </div>
              </div>
            </div>

            <div class="stats stats-vertical md:stats-horizontal shadow">
              <div class="stat">
                <div class="stat-title text-primary">Name</div>
                <div class="stat-value text-base text-neutral">
                  {{ selectedCharacter.name }}
                </div>
              </div>

              <div class="stat">
                <div class="stat-title text-primary">Status</div>
                <div class="stat-value text-base text-neutral">
                  {{ selectedCharacter.status }}
                </div>
              </div>

              <div class="stat">
                <div class="stat-title text-primary">Species</div>
                <div class="stat-value text-base text-neutral">
                  {{ selectedCharacter.species }}
                </div>
              </div>

              <div class="stat">
                <div class="stat-title text-primary">Gender</div>
                <div class="stat-value text-base text-neutral">
                  {{ selectedCharacter.gender }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-else class="flex justify-center">
        <span class="loading loading-dots loading-md"></span>
      </div>
    </div>

    <div class="modal-backdrop">
      <button @click="closeModal"></button>
    </div>
  </dialog>
</template>
