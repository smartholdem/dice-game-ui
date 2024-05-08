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
      amount: 10,
      wallet: {
        address: "",
        balance: 0
      }
    }
  },
  methods: {

    async sendCoins(memo) {

    }
  }
}


</script>

<template>
  <div class="greetings">
    <h3 class="green">{{ address }}</h3>
    <h3>STH 0</h3>
    <button @click="sendCoins(127)" class="btn">ROLL 127<</button>
    <input v-model="amount" type="text">
    <button @click="sendCoins(128)" class="btn"> ROLL > 128</button>
  </div>
</template>

<style scoped>
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
