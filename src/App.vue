<script>
import HelloWorld from './components/HelloWorld.vue';
import TheWelcome from './components/TheWelcome.vue';
import { validateMnemonic } from "bip39";
import { Identities } from "@smartholdem/crypto";


export default {
  components: {
    HelloWorld,
    TheWelcome
  },
  data() {
    return {
      gameBank: "SRcse8VH9uqC4DterMfghvtaHdLhHJ9Gi2",
      userAddress: null,
      secret: "",
    }
  },
  methods: {
    async addressFromPassword(secret) {
      const isBip39 = validateMnemonic(secret);
      const address = Identities.Address.fromPassphrase(secret, 63);
      this.userAddress = {
        isBip39: isBip39,
        address: address
      };
      return this.userAddress;
    }
  }
}

</script>

<template>
  <header>
    <img alt="STH logo" class="logo" src="./assets/logo.png" width="96" height="96" />
    <div v-if="userAddress" class="wrapper">
      <HelloWorld :address=userAddress.address />
    </div>
    <div v-else class="wrapper">
      <input @input="" v-model="secret">
      <button @click="addressFromPassword(secret)">Import Address</button>
    </div>
  </header>

  <main>
    <TheWelcome />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
