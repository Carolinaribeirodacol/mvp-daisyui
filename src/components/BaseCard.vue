<script setup>
import { computed } from 'vue'

const props = defineProps({
  name: String,
  episode: String,
  characters: Array,
  airDate: String,
})

const charactersFilter = computed(() => {
  if (props.characters.length > 5) {
    return props.characters.splice(0, 5)
  }

  return props.characters
})

const charactersCount = computed(() => {
  return props.characters.length > 5 ? props.characters.length - 5 : 0
})

function getImage(url) {
  const newUrl = url.replace(
    'https://rickandmortyapi.com/api/character/',
    'https://rickandmortyapi.com/api/character/avatar/',
  )

  return `${newUrl}.jpeg`
}

const firstCharacterImage = computed(() => {
  if (!props.characters.length) return null
  return getImage(props.characters[0])
})
</script>

<template>
  <div class="card bg-base-100 shadow-sm hover:bg-neutral-content hover:shadow-xl cursor-pointer">
    <figure v-if="firstCharacterImage">
      <img :src="firstCharacterImage" alt="first-character-image" />
    </figure>

    <div class="avatar-group -space-x-6 m-auto pt-4">
      <div class="avatar" v-for="(character, index) in charactersFilter" :key="index">
        <div class="w-12">
          <img :src="getImage(character)" alt="character-image" />
        </div>
      </div>

      <div className="avatar avatar-placeholder" v-if="charactersCount">
        <div className="bg-neutral text-neutral-content w-12">
          <span>+{{ charactersCount }}</span>
        </div>
      </div>
    </div>

    <div class="card-body">
      <h2 class="card-title">
        {{ name }}
        <div class="badge badge-secondary text-white">{{ episode }}</div>
      </h2>

      <div class="card-actions justify-end">
        <div class="badge badge-outline">{{ airDate }}</div>
      </div>
    </div>
  </div>
</template>
