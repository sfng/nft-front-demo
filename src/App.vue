<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <WalletTest msg="NTF Contract Test"/>
</template>

<script>
import WalletTest from './components/Wallet.vue'

export default {
  name: 'App',
  components: {
    WalletTest
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
