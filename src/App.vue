<template>
  <div>
    <h1>Get closer and play Bonneteau !</h1>
    <div id="money">Current wallet: {{ money }}$</div>
    <div id="game">
      <Shell v-for="(index) in shells" :disabled="disableShells" :key="index"
             :class="{ 'animate-shell': shuffle }"
             :showPearl="showResult && correctIndex === index" @guess="checkGuess(index)"/>
    </div>
    <p id="message">{{ message }}</p>
    <button @click="playAgain">Bet 10$</button>
  </div>
</template>

<script>
import Shell from './components/Shell.vue';

export default {
  components: {
    Shell
  },
  data() {
    return {
      shells: [0, 1, 2],
      money: 30,
      disableShells: false,
      shuffle: true,
      winStreak: 0,
      firstGame: true,
      correctIndex: null,
      showResult: false,
      message: ''
    };
  },
  methods: {
    fetchRandomNumber() {
      this.shuffle = true;
      fetch("https://www.random.org/integers/?num=1&min=0&max=2&col=1&base=10&format=plain&rnd=new")
          .then(response => response.text())
          .then(text => {
            this.disableShells = false;
            this.correctIndex = parseInt(text.trim(), 10);
            this.showResult = false;
            this.message = "Find the perl !";

            setTimeout(() => {
              this.shuffle = false;
            }, 535);
          })
          .catch(() => {
            this.message = "Couldn't fetch number";
          });
    },
    checkGuess(index) {
      this.showResult = true;
      if (index === this.correctIndex) {
        if (this.winStreak === 3) {
          this.message = "GIVE ME THAT AND GET AWAY YOU CHEATER"
          this.money = 0;
        }

        this.money += 20;
        this.winStreak += 1;
        this.message = "Mmmmh, okay you won...";
      } else {
        this.winStreak = 0;
        this.message = "Thank you for the 10 bucks";
      }

      this.disableShells = true;
    },
    playAgain() {
      if (this.money <= 0) {
        this.message = "You can't play anymore, loser"
        this.lost = true;
        return;
      }
      if (!this.firstGame && !this.showResult) {
        this.message = "Guess before playing again dummy"
        return;
      }

      this.firstGame = false;
      this.money -= 10;
      this.message = '';

      this.fetchRandomNumber();
    }
  },
  created() {
    this.playAgain();
  }
}
</script>

<style scoped>
.animate-shell {
  animation: shuffle 1s infinite;
}
</style>
