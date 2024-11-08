<template>
    <div class="pokemon-card">
      <img 
        :src="pokemon.image" 
        :class="{ 'blurred': !pokemon.nameGuessed }" 
        alt="Pokemon Image" 
      />
      <div v-if="!pokemon.nameGuessed">
        <input v-model="userGuess" placeholder="Adivina el nombre" />
        <button @click="checkName">Comprobar</button>
      </div>
      <p v-else>{{ pokemon.name }}</p>
    </div>
  </template>
  
  <script>
  export default {
    props: ['pokemon'],
    data() {
      return {
        userGuess: ''
      };
    },
    methods: {
      checkName() {
        if (this.userGuess.toLowerCase() === this.pokemon.name.toLowerCase()) {
          this.pokemon.nameGuessed = true;
          this.$emit('correctGuess', this.pokemon.name);
        } else {
          alert("Nombre incorrecto, intenta de nuevo!");
        }
      }
    },
    watch: {
      // Resetear el input si se cambia el Pokémon
      pokemon() {
        this.userGuess = '';
      }
    }
  };
  </script>
  
  <style scoped>
  .pokemon-card {
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 16px;
    text-align: center;
    width: 165px;
    margin: 10px;
  }
  .blurred {
    filter: blur(5px);
  }
  </style>
  