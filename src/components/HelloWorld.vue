<template>
  <div class="greetings">
    <h3 class="green">{{ address }}</h3>
    <h3 class="balance">STH {{(walletBalance).toFixed(8)}}</h3>
    <div v-if="step === 0">
    <button :disabled="walletBalance < 3" @click="sendCoinsInGame(127)" class="btn">ROLL 127<</button>
    <input v-model="amount" type="text">
    <button :disabled="walletBalance < 3" @click="sendCoinsInGame(128)" class="btn"> ROLL > 128</button>
    <p><small>min 2 STH max 500 STH, bid fee 1 STH</small></p>
    </div>

    <div v-if="step === 1">
      please wait confirmation ...
    </div>

  </div>
</template>
<script>
import axios from 'axios';
import { Transactions, Managers, Utils} from "@smartholdem/crypto";
import { Connection } from "@smartholdem/client";


const client = new Connection("https://node1.smartholdem.io/api");
Managers.configManager.setFromPreset("mainnet");
Managers.configManager.setHeight(596000);

export default {
  props: ({
    address: {
      type: String,
      required: true
    },
    secret: {
      type: String,
      required: true
    }
  }),
  data() {
    return {
      step: 0,
      gameBank: "SRcse8VH9uqC4DterMfghvtaHdLhHJ9Gi2",
      amount: 10,
      walletBalance: 0,
      wallet: {},
      timer: null,
    }
  },
  methods: {
    //получить информацию о кошельке
    async getWallet(userId) {
      try {
        this.wallet = (await axios.get('https://node0.smartholdem.io/api/wallets/' + userId)).data.data;
        this.walletBalance = this.wallet.balance / 10 ** 8
      } catch(e) {
        console.log('err get wallet info')
      }
    },
    async txTransfer(payload) {
      const txs = [];
      const senderWallet = await client.api("wallets").get(payload.sender);
      const senderNonce = Utils.BigNumber.make(senderWallet.body.data.nonce).plus(1);
      const transaction = Transactions.BuilderFactory.transfer()
          .fee((1e8).toString())
          .version(2)
          .nonce(senderNonce.toFixed())
          .recipientId(payload.recipientId)
          .amount((payload.amount * 1e8).toFixed(0))
          .vendorField(payload.memo)
          .sign(payload.secret);
      txs.push(transaction.build().toJson());
      console.log('Prepared tx', txs[0]);
      let broadcastResponse = {};
      try {
        broadcastResponse = (await client.api("transactions").create({ transactions: txs })).body.data;
      } catch (e) {
        console.log("err: tx send");
      }
      return broadcastResponse;
    },
    //сделать ставку в блокчейне
    async sendCoinsInGame(amount, memo) {
      const txResult = await this.txTransfer({
        amount: amount,
        memo: memo,
        sender: this.address,
        secret: this.secret
      });
      console.log(txResult);

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

.balance {
  color: #fff;
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
