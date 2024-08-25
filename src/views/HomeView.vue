<template>
  <main class="w-full text-white flex justify-center gap-y-20 flex-col">
    <header class="w-full text-white flex justify-center flex-col items-center h-56 gap-y-5">
      <h1 class="text-6xl font-bold">A la recherche d'un jeu à jouer ?</h1>
      <h2 class="text-3xl">Laissons le hasard choisir pour toi !</h2>
    </header>
    <section v-if="cards.length > 0" class="w-full text-white flex justify-center gap-x-20">
      <article
          v-for="card in cards"
          :key="card.id"
          @click="flipCard(card.id)"
          class="card cursor-pointer w-48 h-80 rounded-xl transition-transform hover:animate-wiggle"
      >
        <div class="relative h-full w-full perspective-1000">
          <div
              :class="{
            'absolute w-full h-full flex justify-center items-center transition-transform': true,
            'animate-flipCard': card.flipped
          }"
              class="relative transform-style-preserve-3d bg-blue-400 rounded-xl"
          >
            <Transition name="fade">
              <div
                  v-if="!card.flipped"
                  class="rounded-xl absolute w-full h-full flex justify-center items-center text-center text-blue-700 text-8xl font-bold backface-hidden"
              >
                ?
              </div>
            </Transition>
            <Transition name="fade">
              <div
                  v-if="card.flipped"
                  class="rounded-xl absolute w-full h-full flex justify-center items-center text-center text-white text-xl font-bold transform rotate-y-180 backface-hidden"
              >
              <span class=" w-8/12">
                  {{ card.content }}
              </span>
              </div>
            </Transition>
          </div>
        </div>
      </article>
    </section>
    <section v-else>
      <h2 class="text-xl text-center">Ajoutez des jeux à la liste pour commencer</h2>
    </section>
    <RouterLink  to="/addgame" class="bg-blue-600 w-fit px-8 py-5 place-self-center rounded-lg text-blue-200 font-bold hover:bg-blue-500 transition duration-200">Ajouter des jeux à la liste</RouterLink>
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface Card {
  id: number;
  content: string;
  flipped: boolean;
}

const cards = ref<Card[]>([]);
const games = ref<{ id: number; name: string }[]>([]);

const chooseGame = (countGame: number): { id: number; name: string }[] => {
  if (games.value.length > 0) {
    const gamesCopy = [...games.value];
    const randomGames: { id: number; name: string }[] = [];
    for (let i = 0; i < countGame; i++) {
      const randomIndex = Math.floor(Math.random() * gamesCopy.length);
      randomGames.push(gamesCopy[randomIndex]);
      gamesCopy.splice(randomIndex, 1);
    }
    return randomGames;
  }
  return [];
};

const initGame = () => {
  const gamesFromStorage = localStorage.getItem('games');
  if (gamesFromStorage) {
    try {
      const gamesToAdd: { id: number; name: string }[] = JSON.parse(gamesFromStorage);
      if (Array.isArray(gamesToAdd) && gamesToAdd.length > 0) {
        games.value = gamesToAdd;
        cards.value = chooseGame(3).map((game, index) => ({
          id: index,
          content: game.name,
          flipped: false,
        }));
      } else {
        cards.value = [];
      }
    } catch (e) {
      cards.value = [];
    }
  } else {
    cards.value = [];
  }
};

initGame();

const flipCard = (id: number) => {
  const card = cards.value.find(card => card.id === id);
  if (card) {
    card.flipped = !card.flipped;
  }
};
</script>
