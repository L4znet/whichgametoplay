<template>
  <main class="w-full text-white flex justify-center gap-y-10 flex-col items-center">
    <header class="w-full text-white flex justify-center flex-col items-center h-56 gap-y-5">
      <h1 class="text-6xl font-bold">Listez les jeux qui seront tirés au sort</h1>
      <h2 class="text-3xl">Séparez les jeux par une virgule</h2>
    </header>

    <Transition name="fade">
      <div v-if="errorMessages.length > 0" class="w-5/12 justify-center flex flex-col items-center">
        <div
            v-for="(message, index) in errorMessages"
            :key="index"
            class="bg-red-500 text-white p-5 rounded-lg w-fit text-center mb-2"
        >
          {{ message }}
        </div>
      </div>
    </Transition>

    <textarea
        v-model="gamesInput"
        class="w-5/12 h-56 rounded-2xl bg-blue-500 text-lg outline-none text-white p-8 placeholder:text-white"
        placeholder="Listez les jeux, en les séparant avec une virgule, comme ceci : Elden Ring, GTA,"
    ></textarea>
    <button
        @click="addGames"
        class="bg-blue-600 w-fit px-8 py-5 place-self-center rounded-lg text-blue-200 font-bold hover:bg-blue-500 transition duration-200"
    >
      Ajouter les jeux
    </button>
  </main>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';

// State variables
const gamesInput = ref<string>('');
const savedGames = ref<{ id: number; name: string }[]>([]);
const errorMessages = ref<string[]>([]);

// Load games from localStorage
const loadGames = () => {
  const storedGames = localStorage.getItem('games');
  if (storedGames) {
    try {
      const parsedGames = JSON.parse(storedGames);
      if (Array.isArray(parsedGames)) {
        savedGames.value = parsedGames;
        gamesInput.value = savedGames.value.map(game => game.name).join(', ');
      }
    } catch (e) {
      savedGames.value = [];
    }
  }
};

// Validate input and update error messages
const validateGamesInput = computed(() => {
  return gamesInput.value.split(',').map(game => game.trim()).filter(game => game);
});

const checks = () => {
  errorMessages.value = []; // Reset error messages

  const gamesArray = validateGamesInput.value;

  if (gamesArray.length === 0) {
    errorMessages.value.push('Vous ne pouvez pas laisser ce champ vide');
  }

  if (gamesArray.length < 3) {
    errorMessages.value.push('Vous devez saisir au moins 3 jeux');
  }

  const uniqueGames = new Set(gamesArray);
  if (gamesArray.length !== uniqueGames.size) {
    errorMessages.value.push('Vous ne pouvez pas ajouter plusieurs fois le même jeu');
  }
};

const addGames = () => {
  checks();

  if (errorMessages.value.length === 0) {
    const gamesArray = validateGamesInput.value.map((game, index) => ({
      id: index,
      name: game,
    }));
    savedGames.value = gamesArray;
    localStorage.setItem('games', JSON.stringify(savedGames.value));
    loadGames();
  }
};

// Load games on component mount
loadGames();
</script>

