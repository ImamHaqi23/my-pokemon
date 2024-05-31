<template>
  <div class="bg-gradient-to-b from-slate-600 to-slate-800">
    <div class="container bg-slate-900 min-h-screen flex-column py-10">
      <div class="flex flex-col justify-center items-center mx-auto relative">
        <img :src="imgTitle" class="w-1/2" alt="Title Pokemon" />
        <h1 class="text-white absolute bottom-28 text-4xl font-semibold">
          Gotta catch'e all !!
        </h1>
      </div>
      <div class="flex justify-center">
        <div class="flex flex-col w-1/2">
          <input
            class="px-4 py-2 rounded-md"
            type="text"
            placeholder="Search Pokemon"
            v-model="search"
          />
          <div class="mt-4">
            <span
              class="bg-slate-500 p-1 rounded mr-1 text-white uppercase hover:bg-slate-400 cursor-pointer"
              v-for="i in suggestedPokemon"
              @click="viewDetailPokemon(i.name)"
              >{{ i.name }}
            </span>
          </div>
        </div>
      </div>

      <div>
        <div
          class="view-all grid grid-cols-4 gap-4 mt-8 mx-10"
          v-if="!viewDetail"
        >
          <div
            v-for="(pokemon, index) in pokemonList"
            :key="index"
            class="rounded-lg p-4 bg-slate-500 shadow-md shadow-slate-500/50 hover:bg-slate-400 cursor-pointer"
            @click="viewDetailPokemon(pokemon.name)"
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
        <div class="view-detail" v-else>
          <div class="mx-2">
            <button
              class="bg-slate-500 p-2 rounded hover:bg-slate-400 text-lg font-semibold text-white cursor-pointer"
              @click="viewDetail = false"
            >
              View All Pokemon
            </button>
          </div>
          <div class="grid grid-cols-12 text-white mt-5 gap-5">
            <div
              class="col-span-6 rounded-lg p-4 bg-slate-500 shadow-md shadow-slate-500/50 ml-5"
            >
              <img
                :src="detailPokemon.sprites.front_default"
                :alt="detailPokemon.name"
                class="w-[500px] h-auto mx-auto"
              />
            </div>
            <div class="col-span-6 mr-5">
              <h1 class="text-3xl font-semibold uppercase">
                {{ detailPokemon.name }}
              </h1>
              <div class="my-1">
                <label>Type : </label>
                <span v-for="i in detailPokemon.types">
                  <span class="bg-slate-600 p-1 rounded mx-1 uppercase">{{
                    i.type.name
                  }}</span>
                </span>
              </div>
              <div class="my-1">
                <label>Skills : </label>
                <span
                  v-for="(move, index) in detailPokemon.moves.slice(0, 10)"
                  class="uppercase"
                >
                  {{ move.move.name }}{{ index < 9 ? ' |' : '' }}
                </span>
                <span v-if="detailPokemon.moves.length > 10">... (More)</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import imgTitle from './assets/titlePoke.png';
import { ref, onMounted, watch } from 'vue';

const pokemonList = ref([]);
const viewDetail = ref(false);
const detailPokemon = ref(null);
const search = ref('');
const filterPokemon = ref([]);
const suggestedPokemon = ref('');

async function getPokemon() {
  try {
    const response = await fetch('https://pokeapi.co/api/v2/pokemon');
    const data = await response.json();
    const results = data.results;

    for (const result of results) {
      await getPokemonByName(result.name);
    }
  } catch (error) {
    console.error('Error fetching Pokemon:', error);
  }
}

async function getPokemonByName(name) {
  try {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
    const data = await response.json();
    pokemonList.value.push(data);
    pokemonList.value.sort((a, b) => a.id - b.id);
  } catch (error) {
    console.error('Error fetching Pokemon by name:', error);
  }
}

const viewDetailPokemon = async (pokemonName) => {
  try {
    const response = await fetch(
      `https://pokeapi.co/api/v2/pokemon/${pokemonName}`
    );
    const data = await response.json();
    console.log(data);
    detailPokemon.value = data;
    viewDetail.value = true;
  } catch (error) {
    console.error('Error fetching Pokemon details:', error);
    detailPokemon.value = null;
  }
};

async function searchPokemon() {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=200');
  const data = await response.json();
  const results = data.results;
  console.log(results);

  filterPokemon.value = results;
}

watch(search, () => {
  let filteredPokemon = filterPokemon.value.filter((pokemon) => {
    return pokemon.name.includes(search.value);
  });
  suggestedPokemon.value = filteredPokemon.slice(0, 7);
});

onMounted(() => {
  getPokemon();
  searchPokemon();
});
</script>
