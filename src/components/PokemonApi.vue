<template>
  <section>
    <h1>¿Quién es ese Pokémon?</h1>
    <p>Pokémon descubiertos: {{ discoveredCount }}</p>
    <div class="row">
      <div class="card" v-for="(pokemon, index) in pokemons" :key="index">
        <img 
          :src="pokemon.image" 
          alt="Pokemon Image" 
          :class="{ 'blurred-image': !pokemon.isDiscovered }" 
        />
        <div v-if="!pokemon.isDiscovered">
          <!--<legend>{{ pokemon.name }}</legend>-->
          <input type="text" v-model="pokemon.inputName" /> <br />
          <button @click="discoverPokemon(index)">Descubrir</button>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PokemonApi',
  data() {
    return {
      pokemons: [],
      discoveredCount: 0
    };
  },
  async mounted() {
    await this.getPokemons();
  },
  methods: {
    async getPokemons() {
      const URL_BASE = "https://pokeapi.co/api/v2/pokemon/";
      const responseApi = await axios.get(`${URL_BASE}?limit=12`);
      const primerLlamado = responseApi.data.results;

      const pokemonsResponse = await Promise.all(
        primerLlamado.map(async (pokemon) => {
          const { data } = await axios.get(pokemon.url);
          return {
            name: data.name,
            image: data.sprites.front_default,
            inputName: '',
            isDiscovered: false
          };
        })
      );

      this.pokemons = pokemonsResponse;
    },
    discoverPokemon(index) {
      const pokemon = this.pokemons[index];
      if (pokemon.inputName.toLowerCase() === pokemon.name.toLowerCase()) {
        pokemon.isDiscovered = true;
        this.discoveredCount++;
      } else {
        alert(`No, este no es ${pokemon.inputName}. Este es ${pokemon.name}`);
      }
    }
  }
};
</script>

<style>
.card {
  width: 200px;
  height: 250px;
  background-color: rgb(197, 245, 126);
  border: 3px solid rgb(255, 255, 255);
  border-radius: 10px;
  margin: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-family: Century Gothic;
}

.blurred-image {
  filter: blur(5px) grayscale(100%);
}

.row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  padding: 20px;
}
</style>
