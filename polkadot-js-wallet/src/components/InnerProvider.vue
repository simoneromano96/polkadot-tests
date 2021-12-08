<script setup lang="ts">
import { reactive, ref } from "vue";

import { ApiPromise, WsProvider } from "@polkadot/api";
import { web3Accounts, web3Enable, web3FromAddress, web3ListRpcProviders, web3UseRpcProvider } from "@polkadot/extension-dapp";

import type { InjectedAccountWithMeta } from "@polkadot/extension-inject/types";

const RPC_URL = "wss://westend-rpc.polkadot.io";

const wsProvider = new WsProvider(RPC_URL);
const api = await ApiPromise.create({ provider: wsProvider });

// returns an array of all the injected sources
// (this needs to be called first, before other requests)
const enable = async () => {
  try {
    const allInjected = await web3Enable("my cool dapp");
    console.log(allInjected);
  } catch (error) {}
};

const accounts = ref<InjectedAccountWithMeta[]>([]);
const getAccounts = async () => {
  // meta.source contains the name of the extension that provides this account
  const allAccounts = await web3Accounts();
  accounts.value = allAccounts;
};

const signTransaction = async () => {
  //   // finds an injector for an address

  const currentAccounts = accounts.value;
  const sender = currentAccounts.at(0);
  if (!sender) {
    throw new Error("Missing sender address");
  }
  const destination = currentAccounts.at(1);
  if (!destination) {
    throw new Error("Missing destination address");
  }
  const injector = await web3FromAddress(sender.address);

  const signed = await api.tx.balances.transfer(destination.address, 0.1).signAsync(sender.address, { signer: injector.signer });
  console.log("🚀 ~ file: InnerProvider.vue ~ line 45 ~ signMessage ~ signed", signed);
};
</script>

<template>
  <button @click="enable">Connect Polkadot JS</button>
  <button @click="getAccounts">Get my accounts</button>
  <button @click="signTransaction">Sign transaction</button>
</template>
