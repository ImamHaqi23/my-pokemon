<template>
  <div class="bg-gradient-to-b from-slate-600 to-slate-800">
    <div class="container bg-slate-900 h-full flex-column py-10">
      <div class="flex justify-center mx-auto">
        <img :src="imgTitle" class="w-1/3" alt="Title Pokemon" />
      </div>
      <div class="flex justify-center">
        <input
          class="px-4 py-2 w-1/4 rounded-md"
          type="text"
          placeholder="Search Pokemon"
        />
      </div>
      <div class="grid grid-cols-4 gap-4 mt-8 mx-10">
        <div
          v-for="(pokemon, index) in pokemonList"
          :key="index"
          class="rounded-lg p-4 bg-slate-500 shadow-md shadow-slate-500/50"
        >
          <img
            :src="pokemon.sprites.front_default"
            :alt="pokemon.name"
            class="mx-auto w-52"
          />
          <p
            class="text-center mt-2 uppercase text-lg font-semibold text-white"
          >
            {{ pokemon.name }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import imgTitle from './assets/titlePoke.png';
import { ref, onMounted } from 'vue';

const pokemonList = ref([]);

async function getPokemon() {
  try {
    const response = await fetch('https://pokeapi.co/api/v2/pokemon');
    const data = await response.json();
    const results = data.results;

    for (const result of results) {
      await getPokemonByNmae(result.name);
    }
  } catch (error) {
    console.error('Error fetching Pokemon:', error);
  }
}

async function getPokemonByNmae(result) {
  try {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${result}`);
    const data = await response.json();
    pokemonList.value.push(data);
    pokemonList.value.sort((a, b) => a.id - b.id);
  } catch (error) {
    console.error('Error fetching Pokemon by ID:', error);
  }
}
onMounted(() => {
  getPokemon();
});
</script>
