<template>
  <div>

    There are {{ numberOfHorses }} horses. How many races do you need to identify the fastest 3 horses ? You can race up to {{ horsesPerRace }} horses at a time.
    <br>
    <button
      v-on:click="generateHorses">
      Whistle for the horses
    </button>

    <!-- main content -->
    <div v-if="horseWhistled">
      <div>
        Here they are ! Chose who you wish to race :
        <br>
        <button
          v-for="horse in horseList" :key="horse.name"
          :disabled="isRaceReady"
          v-on:click="toggleRaceParticipation(horse)">
          {{ horse.name }}
        </button>
      </div>
      <div>
        <table>
          <tr v-for="horse in raceList" :key="horse.name">
            <td>{{ horse.name }}</td>
          </tr>
        </table>
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
      horsesPerRace: 5,
      horseList: [],
      raceList: []
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
      if (this.raceList.includes(horse)) this.raceList.splice(this.raceList.indexOf(horse), 1) // remove horse from the list if already in it
      else this.raceList.push(horse)
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
  .horseButton {
    display: inline;
    color: red;
  }
  @media only screen and (max-width: 600px) {
    body {
      margin: 20px;
    }
  }

</style>
