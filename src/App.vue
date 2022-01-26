<template>
  <h1>GIS Memory Game!</h1>
  <section class="game-board">
    <Card v-for="(card, index) in cardList" :key="`card-${index}`" :value="card" />
  </section>
</template>

<script>
import Card from './components/Card.vue'

export default {
  name: 'App',
  components: {
    Card
  },
  setup() {
    const cardList = []

    for (let i = 0; i < 16; i++) {
      cardList.push(i)
    }

    return {
      cardList
    }
  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.card {
  border: 5px solid #ccc;
}

.game-board {
  display: grid;
  grid-template-columns: 15vw 15vw 15vw 15vw;
  grid-template-rows: 10vh 10vh 10vh 10vh;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}
</style>
