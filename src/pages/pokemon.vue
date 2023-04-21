<script setup>
const pokemonList = ref([])

onMounted(async () => {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151')
  const data = await response.json()
  const mappedData = data.results.map((pokemon, index) => ({
    name: pokemon.name,
    sprite: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${index + 1}.png`,
  }))
  pokemonList.value = mappedData
})
</script>

<template>
  <div class="pokemon-grid">
    <div v-for="pokemon in pokemonList" :key="pokemon.name" class="pokemon">
      <img :src="pokemon.sprite" alt="pokemon sprite">
      <span>{{ pokemon.name }}</span>
    </div>
  </div>
</template>

<style scoped>
.pokemon-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.pokemon {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}
</style>
