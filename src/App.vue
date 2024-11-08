<template>
  <div id="app">
    <h1>¿Quién es ese <span class="pokemon">Pokémon</span>?</h1>
    <p>Pokémon adivinados: <span class="count">{{ guessedCount }}</span></p>
    <button @click="loadNewPokemon">Cambiar Pokémon</button>
    <button @click="resetCounter">Reiniciar Juego</button>
    <div class="pokemon-grid">
      <PokemonCard 
        v-for="(pokemon, index) in filteredPokemonList" 
        :key="pokemon.name" 
        :pokemon="pokemon" 
        @correctGuess="incrementGuessedCount"
      />
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import PokemonCard from './components/PokemonCard.vue';

export default {
  components: {
    PokemonCard
  },
  data() {
    return {
      pokemonList: [],
      guessedPokemon: new Set(),
      guessedCount: 0
    };
  },
  computed: {
    filteredPokemonList() {
      return this.pokemonList.map(pokemon => ({
        ...pokemon,
        nameGuessed: this.guessedPokemon.has(pokemon.name)
      }));
    }
  },
  methods: {
    async loadNewPokemon() {
      try {
        const offset = Math.floor(Math.random() * 1025);
        const response = await axios.get(`https://pokeapi.co/api/v2/pokemon?limit=20&offset=${offset}`);
        
        const newPokemonData = await Promise.all(
          response.data.results.map(async (pokemon) => {
            const pokemonDetails = await axios.get(pokemon.url);
            return {
              name: pokemon.name,
              image: pokemonDetails.data.sprites.front_default,
              nameGuessed: this.guessedPokemon.has(pokemon.name)
            };
          })
        );

        this.pokemonList = newPokemonData;
      } catch (error) {
        console.error("Error al cargar los datos de Pokémon:", error);
      }
    },
    incrementGuessedCount(pokemonName) {
      if (!this.guessedPokemon.has(pokemonName)) {
        this.guessedCount++;
        this.guessedPokemon.add(pokemonName);
      }
    },
    resetCounter() {
      this.guessedCount = 0;
      this.guessedPokemon.clear();
      this.loadNewPokemon();
    }
  },
  mounted() {
    this.loadNewPokemon();
  }
};
</script>

<style>
@import url('https://fonts.cdnfonts.com/css/pokemon-solid');

h1, p {
  margin-top: -20px;
}

#app {
  text-align: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

button {
  margin: 8px;
  padding: 8px 16px;
  background-color: black;
  box-shadow: 10px 5px 5px gray;
  color: white;
  font-size: 16px;
  border-radius: 10px;
}

.pokemon{
  font-size: 36px;
  color: yellow;
  -webkit-text-stroke: 1px blue;
  font-family: 'Pokemon Solid', sans-serif;
  letter-spacing: 4px;
}

.count {
  color: darkkhaki;
  font-weight: bold;
  font-size: 20px;
}

.pokemon-grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
</style>

