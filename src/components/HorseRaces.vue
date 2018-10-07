<template>
  <div>
    <h2>
      There are
      <span class="highlighted">
        {{ numberOfHorses }}
      </span>
      horses. You have to buy the fastest
      <span class="highlighted">
        {{ numberOfHorsesToBuy }}
      </span>
      of them.
      <br>
      You can race up to
      <span class="highlighted">
        {{ horsesPerRace }}
      </span>
        horses at a time. How many races do you need ?</h2>
    <br><br>
    <button
      v-on:click="generateHorses">
      Whistle for the horses
    </button>
    <br><br>
    <button
      v-for="horse in horseList" :key="horse.name"
      :disabled="!raceMode && !buyMode"
      v-on:click="select(horse)">
      {{ horse.name }}
    </button>
    <br><br>
    <button
      v-if="horseWhistled"
      v-on:click="enableRaceMode"
      :disabled="raceMode">
      Make a race
    </button>
    <button
      v-if="horseWhistled"
      v-on:click="enableBuyMode"
      :disabled="buyMode">
      Buy the horses
    </button>

    <!-- New race -->
    <div v-if="raceMode">
      <h4>Click on the horses you wish to race !</h4>
      <h4>Line up:</h4>
      <button
        class="raceLineUpButton"
        v-for="horse in raceList" :key="horse.name"
        v-on:click="toggleRaceParticipation(horse)">
        {{ horse.name }}
      </button>
      <br>
      <button
          v-if="isRaceReady"
          v-on:click="startRace">
          Start race !
      </button>
    </div>

    <!-- Buy horses -->
    <div v-if="buyMode">
      <h4>Click on the horses you wish to buy !</h4>
      <h4>Horse cart:</h4>
      <button
        class="cartButton"
        v-for="horse in horseCart" :key="horse.name"
        v-on:click="toggleCartParticipation(horse)">
        {{ horse.name }}
      </button>
      <br>
      <button
        v-if="isCartReady"
        v-on:click="buyHorses">
        Buy horses !
      </button>
    </div>

    <!-- Previous race results -->
    <br>
    <div
      class="raceResult"
      v-for="(raceResult, index) in raceResults" :key="index">
      <h4>Race #{{ index + 1 }} result:</h4>
      <div
        v-for="(horse, index) in raceResult" :key="horse.name">
        {{ index + 1 }}: {{ horse.name }}
      </div>
    </div>
  </div>
</template>

<script>
import petNames from 'pet-names'
export default {
  data () {
    return {
      numberOfHorses: 25,
      numberOfHorsesToBuy: 3,
      horsesPerRace: 5,
      horseList: [],
      raceList: [],
      horseCart: [],
      fastest3Horses: [],
      raceResults: [],
      raceMode: false,
      buyMode: false,
      raceCounter: 0
    }
  },
  methods: {
    reset () {
      this.horseList = []
      this.raceList = []
      this.raceResults = []
      this.fastest3Horses = []
      this.horseCart = []
      this.raceMode = false
      this.buyMode = false
      this.raceCounter = 0
    },
    generateHorses () {
      this.reset()

      let horseNames = []
      while (horseNames.length < 25) {
        const name = petNames.random()
        if (!horseNames.includes(name)) horseNames.push(name)
      }
      horseNames.map(name => this.horseList.push(
        {
          name: name,
          speed: Math.round(Math.random() * 1000)
        }
      ))
      const orderedHorseList = this.horseList.slice().sort((horse1, horse2) => horse2.speed - horse1.speed)
      orderedHorseList.splice(3)
      this.fastest3Horses = orderedHorseList
    },
    select (horse) {
      if (this.raceMode) this.toggleRaceParticipation(horse)
      if (this.buyMode) this.toggleCartParticipation(horse)
    },
    toggleRaceParticipation (horse) {
      if (this.raceList.includes(horse)) {
        this.raceList.splice(this.raceList.indexOf(horse), 1) // remove horse from the list if already in it
      } else if (!this.isRaceReady) { // do nothing if race line up is full
        this.raceList.push(horse)
      }
    },
    toggleCartParticipation (horse) {
      if (this.horseCart.includes(horse)) {
        this.horseCart.splice(this.horseCart.indexOf(horse), 1)
      } else if (!this.isCartReady) {
        this.horseCart.push(horse)
      }
    },
    startRace () {
      const raceResult = this.raceList.slice().sort((horse1, horse2) => horse2.speed - horse1.speed)
      this.raceList = []
      this.raceResults.push(raceResult)
      this.raceCounter++
    },
    buyHorses () {
      let isCartCorrect = true
      this.horseCart.map((horse) => {
        if (!this.fastest3Horses.includes(horse)) isCartCorrect = false
      })
      let msg
      if (isCartCorrect) {
        if (this.raceCounter === 0) msg = 'Hey ! Stop cheating !'
        else if (this.raceCounter < 7) msg = 'You did buy the right horses, but you shouldn\'t be able to tell with such few races. Consider yourself lucky !'
        else if (this.raceCounter === 7) msg = 'Congratulation ! Seems like you found the optimal solution'
        else if (this.raceCounter <= 11) msg = 'Good job ! But you could have find out with fewer races. Work on it !'
        else if (this.raceCounter > 11) msg = 'Hmm that\'s right, but you\'re not very effective.'
      } else msg = 'Sorry, that was not the 3 fastest horses of the stud. Try again !'
      window.alert(msg)
      this.reset()
    },
    enableRaceMode () {
      this.buyMode = false
      this.raceMode = true
    },
    enableBuyMode () {
      this.raceMode = false
      this.buyMode = true
    }
  },
  computed: {
    horseWhistled () {
      return this.horseList.length > 0
    },
    isRaceReady () {
      return this.raceList.length === this.horsesPerRace
    },
    isCartReady () {
      return this.horseCart.length === this.numberOfHorsesToBuy
    }
  }
}
</script>
<style scoped>
  .highlighted {
    color: violet;
  }
  .raceLineUpButton {
    display: block;
    margin: 5px auto;
  }
  .cartButton {
    display: block;
    margin: 5px auto;
  }
  .raceResult {
    display: inline-block;
    margin: 10px;
  }
  button {
    padding: 7px;
    margin: 3px;
    border: 1px solid #2c3e50;
    border-radius: 3px;
    background-color: transparent;
  }
  button:hover {
    cursor: pointer;
  }
  button:focus{
    outline: none;
  }
  button[disabled] {
    border: 1px solid lightgray;
    color: lightgray;
  }
  button[disabled]:hover {
    border: 1px solid lightgray;
    cursor: default;
  }
</style>
