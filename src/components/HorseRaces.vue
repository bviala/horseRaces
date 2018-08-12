<template>
  <div>
    There are {{ numberOfHorses }} horses. How many races do you need to identify the fastest 3 horses ?
    <br>
    You can race up to {{ horsesPerRace }} horses at a time.
    <br>
    <button
      v-on:click="generateHorses">
      Whistle for the horses
    </button>

    <!-- Previous race results -->
  <!--   <h3>Race results:</h3> -->
    <div
      v-for="(raceResult, raceIndex) in raceResults" :key="raceIndex">
      <h4>Race #{{ raceIndex + 1 }} result:</h4>
      <div
        v-for="(horse, position) in raceResult.order" :key="horse.name">
        {{ position + 1 }}: {{ horse.name }}
      </div>
      <div
        v-for="(inference, index) in raceResult.inferences" :key="index">
        {{ inference }}
      </div>
    </div>

    <!-- New race -->
    <div>
      <h2>Chose who you wish to race next:</h2>

      <div id="raceControl">
        <div id="pool">
          <h3>Pool:</h3>
          <button
            v-for="horse in horseList" :key="horse.name"
            :disabled="isRaceReady"
            v-on:click="toggleRaceParticipation(horse)">
            {{ horse.name }}
          </button>
        </div>

        <div id="lineUp">
          <h3>Line up:</h3>
          <button
            class="raceLineUpButton"
            v-for="horse in raceList" :key="horse.name"
            v-on:click="toggleRaceParticipation(horse)">
            {{ horse.name }}
          </button>
        </div>

      </div>

      <button
          v-if="isRaceReady"
          v-on:click="startRace">
          Start race !
      </button>

    </div>

  </div>
</template>

<script>
import petNames from 'pet-names'
export default {
  data () {
    return {
      numberOfHorses: 25,
      horsesPerRace: 5,
      horseList: [],
      raceList: [],
      raceResults: []
    }
  },
  methods: {
    generateHorses () {
      console.log('Generating Horses')

      // reset
      this.horseList = []
      this.raceList = []

      let horseNames = []
      while (horseNames.length < 25) {
        const name = petNames.random()
        if (!horseNames.includes(name)) horseNames.push(name)
      }
      this.horseList = horseNames.map(name => {
        return {
          name: name,
          speed: Math.round(Math.random() * 1000)
        }
      })
    },
    toggleRaceParticipation (horse) {
      if (this.raceList.includes(horse)) {
        this.raceList.splice(this.raceList.indexOf(horse), 1) // remove horse from the list if already in it
        this.horseList.push(horse)
      } else {
        this.horseList.splice(this.horseList.indexOf(horse), 1)
        this.raceList.push(horse)
      }
    },
    startRace () {
      const order = this.raceList.slice().sort((horse1, horse2) => horse1.speed < horse2.speed)
      this.raceList = []
      const inferences = []
      order.map((horse, index) => {
        if (index < 3) this.horseList.push(horse)
        else inferences.push(`We can eliminate ${horse.name} because he is slower than at least 3 other horses`)
      })
      this.raceResults.push({
        order: order,
        inferences: inferences
      })
    }
  },
  computed: {
    horseWhistled () {
      return this.horseList.length > 0
    },
    isRaceReady () {
      return this.raceList.length === this.horsesPerRace
    }
  }
}
</script>
<style>
  body {
    margin-left: 200px;
    margin-right: 200px;
  }
  #raceControl {
    display: flex;
  }
  #pool {
    flex: 2;
  }
  #lineUp {
    flex: 1;
  }
  .raceLineUpButton{
    display: block;
    color: red;
    margin: auto;
  }
  @media only screen and (max-width: 600px) {
    body {
      margin: 20px;
    }
  }

</style>
