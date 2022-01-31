<template>
  <h1>GIS Memory Game!</h1>
  <transition-group
   tag="section"
    class="game-board"
     name="shuffle-card">
    <Card
      v-for="card in cardList"
      :key="`${card.value}-${card.variant}`"
      :matched="card.matched"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      @select-card="flipCard"
    />
  </transition-group>
  <h2>{{ status }}</h2>
  <button v-if="newPlayer" @click="startGame" class="button">
   <img src="/images/play.svg" alt="Restart Icon" />Start Game
  </button>
  <button v-else @click="restartGame" class="button">
   <img src="/images/restart.svg" alt="Restart Icon" />Restart Game
  </button>
</template>

<script>
import _ from 'lodash'
import { computed, ref, watch } from 'vue'
import { launchConfetti } from './utilities/confetti'
import Card from './components/Card'
export default {
  name: 'App',
  components: {
    Card
  },
  setup() {
    const cardList = ref([])
    const userSelection = ref([])
    const newPlayer = ref(true)

    const startGame = ()=>{
      newPlayer.value = false

      restartGame()
    }


    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return 'Player wins!'
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`
      }
    })
    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        card => card.matched === false
      ).length
      return remainingCards / 2
    })
    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value)
      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false
        }
      })
    }
    const cardItems = [
      'gdal',
      'spatial',
      'qgis',
      'esri',
      'globe',
      'location',
      'maplayers',
      'vector'
    ]
    cardItems.forEach(item => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: false,
        position: null,
        matched: false
      })
      cardList.value.push({
        value: item,
        variant: 2,
        visible: true,
        position: null,
        matched: false
      })
    })
    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index
      }
    })
    const flipCard = payload => {
      cardList.value[payload.position].visible = true
      if (userSelection.value[0]) {
        if (
          userSelection.value[0].position === payload.position &&
          userSelection.value[0].faceValue === payload.faceValue
        ) {
          return
        } else {
          userSelection.value[1] = payload
        }
      } else {
        userSelection.value[0] = payload
      }
    }
    watch(remainingPairs, currentValue => {
      if (currentValue === 0) {
        launchConfetti()
      }
    })
    watch(
      userSelection,
      currentValue => {
        if (currentValue.length === 2) {
          const cardOne = currentValue[0]
          const cardTwo = currentValue[1]
          if (cardOne.faceValue === cardTwo.faceValue) {
            cardList.value[cardOne.position].matched = true
            cardList.value[cardTwo.position].matched = true
          } else {
            setTimeout(() => {
              cardList.value[cardOne.position].visible = false
              cardList.value[cardTwo.position].visible = false
            }, 2000)
          }
          userSelection.value.length = 0
        }
      },
      { deep: true }
    )
    return {
      cardList,
      flipCard,
      userSelection,
      status,
      restartGame,
      startGame,
      newPlayer
    }
  }
}
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}

h1 {
  font-weight: bold;
  color: #1e3b7a;
  font-family: Verdana, Geneva, sans-serif;
}

h2 {
  color: #1e3b7a;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background:linear-gradient(rgba(0,0,0,0.3), rgba(255,255,255,0.3)), url("/images/gis_background_v2.jpg");
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  background-color: #00070c;
  height: 100vh;
  width: 100vw;
  position: absolute;
}

.button {
  background-color: #2989d9;
  color: black;
  font-weight: bold;
  padding: 0.75rem 0.5rem;
  display: flex;
  justify-content: center;
  margin: 0 auto;
  border-color: black;
  border: 0px;
  box-shadow: 0px 5px 10px 1px #322f2f;
  border-radius: 10px;
}

.button img{
  padding-right: 5px;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(4, 9vw);
  grid-template-rows: repeat(4, 15vh);
  grid-column-gap: 5vh;
  grid-row-gap: 1vw;
  justify-content: center;
  margin-top: 5vh;
}

@media only screen and (max-width: 500px) {
  .game-board {
      grid-template-columns: repeat(4, 12vw);
      grid-template-rows: repeat(4, 15vh);
      grid-row-gap: 2vw;
  }
  h1 {
    font-size: 1.5rem;
  }
}


.shuffle-card-move {
  transition: transform 0.8s ease-in;
}
</style>
