<script setup lang="ts">
import { onBeforeUnmount, reactive, ref } from "vue";

import { ApiPromise, WsProvider } from "@polkadot/api";
import { web3Accounts, web3AccountsSubscribe, web3Enable, web3FromAddress, web3ListRpcProviders, web3UseRpcProvider } from "@polkadot/extension-dapp";

import type { InjectedAccountWithMeta, Unsubcall } from "@polkadot/extension-inject/types";

const RPC_URL = "";

const wsProvider = new WsProvider(RPC_URL);
const api = await ApiPromise.create({ provider: wsProvider });

// returns an array of all the injected sources
// (this needs to be called first, before other requests)
let unsubscribe: Unsubcall | undefined;
const accounts = ref<InjectedAccountWithMeta[]>([]);
const enable = async () => {
  try {
    await web3Enable("my cool dapp");
    // we subscribe to any account change and log the new list.
    // note that `web3AccountsSubscribe` returns the function to unsubscribe
    const allAccounts = await web3Accounts();
    accounts.value = allAccounts;

    unsubscribe = await web3AccountsSubscribe((injectedAccounts) => {
      accounts.value = injectedAccounts;
    });
  } catch (error) {
    console.error(error);
  }
};

const signAndSendTransaction = async () => {
  try {
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

    const signed = await api.tx.balances.transfer(destination.address, 0.1).signAndSend(sender.address, { signer: injector.signer });
    console.log("🚀 ~ file: InnerProvider.vue ~ line 45 ~ signMessage ~ signed", signed);
  } catch (error) {
    console.error(error);
  }
};

const getBalance = async () => {
  const currentAccounts = accounts.value;
  const sender = currentAccounts.at(0);
  if (!sender) {
    throw new Error("Missing sender address");
  }
  const {data} = await api.query.system.account(sender.address)
  console.log("🚀 ~ file: InnerProvider.vue ~ line 61 ~ getBalance ~ balance", data.free.toNumber())
};

onBeforeUnmount(() => {
  // console.log("🚀 ~ file: InnerProvider.vue ~ line 49 ~ onBeforeUnmount ~ onBeforeUnmount")
  unsubscribe?.();
});
</script>

<template>
  <button @click="enable">Connect Polkadot JS</button>
  <!-- <button @click="getAccounts">Get my accounts</button> -->
  <button @click="signAndSendTransaction">Sign and send transaction</button>
  <button @click="getBalance">Get balance</button>
</template>
