<template>
  <div>
    <!-- TITLE -->
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
        horses at a time. How many races do you need ?
    </h2>

    <!-- MODE SELECTION -->
    <div class="modeSelection">
      <div class="tab"
        @click="mode = 'race'"
        :class="{ selected: mode === 'race' }">
        Make a race
      </div>
      <div class="tab"
        @click="mode = 'buy'"
        :class="{ selected: mode === 'buy' }">
        Buy the horses
      </div>
    </div>

    <h3>Select the horses you wish to {{ mode }} !</h3>

    <!-- HORSE LIST -->
    <div v-if="mode === 'race'">
      <div class="horseList">
        <button
          v-for="horse in horseList" :key="horse.name"
          @click="toggleRaceParticipation(horse)"
          :disabled="isRaceReady && !raceList.includes(horse)"
          :class="{ selected: raceList.includes(horse) }">
          {{ horse.name }}
        </button>
      </div>

      <button
        :disabled="!isRaceReady"
        @click="startRace">
        Start race !
      </button>
    </div>

    <div v-if="mode === 'buy'">
      <div class="horseList">
        <button
          v-for="horse in horseList" :key="horse.name"
          @click="toggleCartParticipation(horse)"
          :disabled="isCartReady && !horseCart.includes(horse)"
          :class="{ selected: horseCart.includes(horse) }">
          {{ horse.name }}
        </button>
      </div>

      <button
        :disabled="!isCartReady"
        @click="buyHorses">
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
      mode: 'race',
      raceCounter: 0
    }
  },
  created () {
    this.generateHorses()
  },
  methods: {
    reset () {
      this.horseList = []
      this.raceList = []
      this.raceResults = []
      this.fastest3Horses = []
      this.horseCart = []
      this.mode = 'race'
      this.raceCounter = 0
      this.generateHorses()
    },
    generateHorses () {
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
      } else msg = 'Sorry, that was not the 3 fastest horses in the stud. Try again !'
      window.alert(msg)
      this.reset()
    }
  },
  computed: {
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
  .modeSelection {
    margin: 20px;
  }
  .tab {
    margin: 5px;
    padding: 10px;
    display: inline-block;
    font-size: 13pt;
  }
  .tab.selected {
    border-bottom: 2px solid violet;
  }
  .tab:hover {
    cursor: pointer;
  }
  .main {
    display: flex;
  }
  .horseList {
    margin-bottom: 20px;
  }
  .action {
    flex: 1;
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
    margin: 5px;
    border: none;
    background-color: transparent;
  }
  button.selected {
    border-bottom : 1px solid violet;
    /* background-color: #2c3e50;
    color: white; */
  }
  button:hover {
    cursor: pointer;
  }
  button:focus{
    outline: none;
  }
  button:active{
    color: inherit;
  }
  button[disabled] {
    color: lightgray;
  }
  button[disabled]:hover {
    cursor: default;
  }
</style>
