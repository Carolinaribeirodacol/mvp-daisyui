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
const isLoadingCharacter = ref(false)
const loadedCharacters = ref(new Map())

async function handleGetCharacter(characterUrl: string) {
  if (isLoadingCharacter.value) return

  const characterId = characterUrl.split('/').at(-1)

  if (selectedCharacter.value?.id === parseInt(characterId)) return

  if (loadedCharacters.value.has(characterId)) {
    selectedCharacter.value = loadedCharacters.value.get(characterId)
    return
  }

  isLoadingCharacter.value = true

  try {
    const response = await axios.get(`https://rickandmortyapi.com/api/character/${characterId}`)

    loadedCharacters.value.set(characterId, response.data)
    selectedCharacter.value = response.data
  } catch (error) {
    console.log(error)
  } finally {
    isLoadingCharacter.value = false
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
                  {{ episode.characters?.length }}
                </div>
              </div>

              <div class="stat">
                <div v-if="selectedCharacter">
                  <div class="stat-title text-primary mb-2">Character Selected</div>

                  <button
                    @click="selectedCharacter = null"
                    class="btn btn-outline btn-primary btn-sm mb-4"
                  >
                    Voltar
                  </button>

                  <div class="flex flex-col gap-4">
                    <div class="flex justify-center">
                      <div class="avatar">
                        <div
                          class="w-28 rounded-full ring ring-secondary ring-offset-base-100 ring-offset-2"
                        >
                          <img :src="getImage(selectedCharacter.url)" alt="character" />
                        </div>
                      </div>
                    </div>

                    <div class="grid grid-cols-2 gap-2">
                      <div class="bg-base-200 p-3 rounded-lg">
                        <div class="text-xs text-primary font-semibold">Name</div>
                        <div class="text-sm text-neutral">{{ selectedCharacter.name }}</div>
                      </div>

                      <div class="bg-base-200 p-3 rounded-lg">
                        <div class="text-xs text-primary font-semibold">Status</div>
                        <div class="text-sm text-neutral">{{ selectedCharacter.status }}</div>
                      </div>

                      <div class="bg-base-200 p-3 rounded-lg">
                        <div class="text-xs text-primary font-semibold">Species</div>
                        <div class="text-sm text-neutral">{{ selectedCharacter.species }}</div>
                      </div>

                      <div class="bg-base-200 p-3 rounded-lg">
                        <div class="text-xs text-primary font-semibold">Gender</div>
                        <div class="text-sm text-neutral">{{ selectedCharacter.gender }}</div>
                      </div>

                      <div class="bg-base-200 p-3 rounded-lg">
                        <div class="text-xs text-primary font-semibold">Location</div>
                        <div class="text-sm text-neutral">
                          {{ selectedCharacter.location.name }}
                        </div>
                      </div>

                      <div class="bg-base-200 p-3 rounded-lg">
                        <div class="text-xs text-primary font-semibold">Origin</div>
                        <div class="text-sm text-neutral">{{ selectedCharacter.origin.name }}</div>
                      </div>
                    </div>
                  </div>
                </div>

                <div v-else>
                  <div class="stat-title text-primary">Character List</div>

                  <div class="stat-value text-base text-neutral flex flex-wrap gap-2 mt-2">
                    <div
                      v-for="(character, index) in episode.characters"
                      :key="index"
                      class="avatar"
                    >
                      <div
                        class="w-16 rounded-full hover:cursor-pointer transition-transform duration-300 hover:scale-110"
                        :class="{
                          'cursor-not-allowed opacity-50': isLoadingCharacter,
                        }"
                        @click="handleGetCharacter(character)"
                      >
                        <img :src="getImage(character)" alt="character" />
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-else-if="isLoadingCharacter" class="flex justify-center mt-4">
        <span class="loading loading-dots loading-md"></span>
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
