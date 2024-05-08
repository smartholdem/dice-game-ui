<template>
  <div class="greetings">
    <h3 class="green">{{ address }}</h3>
    <h3 v-if="wallet.balance">STH {{(wallet.balance / 10 ** 8).toFixed(8)}}</h3>
    <div v-if="step === 0">
    <button @click="sendCoinsInGame(127)" class="btn">ROLL 127<</button>
    <input v-model="amount" type="text">
    <button @click="sendCoinsInGame(128)" class="btn"> ROLL > 128</button>
    <p><small>min 2 STH max 500 STH, bid fee 1 STH</small></p>
    </div>

    <div v-if="step === 1">
      please wait confirmation ...
    </div>

  </div>
</template>
<script>
import axios from 'axios';

export default {
  props: ({
    address: {
      type: String,
      required: true
    }
  }),
  data() {
    return {
      step: 0,
      gameBank: "SRcse8VH9uqC4DterMfghvtaHdLhHJ9Gi2",
      amount: 10,
      wallet: {
        address: "",
        balance: 0
      },
      timer: null,
    }
  },
  methods: {
    //получить информацию о кошельке
    async getWallet(userId) {
      try {
        this.wallet = (await axios.get('https://node0.smartholdem.io/api/wallets/' + userId)).data.data;
      } catch(e) {
        console.log('err get wallet info')
      }
    },
    //сделать ставку в блокчейне
    async sendCoinsInGame(memo) {
      this.step = 1;
      clearTimeout(this.timer);
      this.timer = setTimeout(async () => {
        this.step = 0;
      }, 10000)

    }
  },
  async mounted() {
    await this.getWallet(this.address); //получить баланс кошелька
  }
}
</script>
<style scoped>
button {
  background: blue;
  color: #fff;
  padding: 5px;
}

h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
