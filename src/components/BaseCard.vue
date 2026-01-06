<script setup>
import { computed } from 'vue'
import Badge from './Badge.vue'
import { onMounted } from 'vue'

const props = defineProps({
  title: String,
  episode: String,
  characters: Array,
  airDate: String,
})

onMounted(() => {
  randomIndex.value = Math.floor(Math.random() * props.characters.length)
})

const emit = defineEmits(['clickEpisode'])

const charactersFilter = computed(() => {
  if (props.characters.length > 5) {
    return props.characters.slice(0, 5)
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

const randomIndex = ref(0)
const randomCharacterImage = computed(() => {
  if (!props.characters.length) return null
  return getImage(props.characters[randomIndex.value])
})
</script>

<template>
  <div
    class="card card-side bg-base-100 shadow-sm hover:bg-neutral-content hover:shadow-xl cursor-pointer"
    @click="emit('clickEpisode')"
  >
    <figure v-if="randomCharacterImage">
      <img :src="randomCharacterImage" alt="random-character-image" />
    </figure>

    <div class="card-body">
      <h2 class="card-title justify-center">
        <span>{{ title }}</span>

        <Badge>
          {{ episode }}
        </Badge>
      </h2>

      <div class="card-actions justify-center">
        <Badge outline>
          {{ airDate }}
        </Badge>
      </div>

      <div class="avatar-group -space-x-6 m-auto">
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
    </div>
  </div>
</template>
