<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import HelloWorld from './components/HelloWorld.vue'

import {
  web3Accounts,
  web3Enable,
  web3FromAddress,
  web3ListRpcProviders,
  web3UseRpcProvider
} from '@polkadot/extension-dapp';

// returns an array of all the injected sources
// (this needs to be called first, before other requests)
const enable = async () => {
  try {
    const allInjected = await web3Enable('my cool dapp');
    console.log(allInjected);
  } catch (error) {
    
  }
}

const getAccounts = async () => {
  // meta.source contains the name of the extension that provides this account
  const allAccounts = await web3Accounts();
  console.log(allAccounts);
}
</script>

<template>
  <button @click="enable">Connect Polkadot JS</button>
  <button @click="getAccounts">Get my accounts</button>
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <!-- <HelloWorld msg="Hello Vue 3 + TypeScript + Vite" /> -->
</template>

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
