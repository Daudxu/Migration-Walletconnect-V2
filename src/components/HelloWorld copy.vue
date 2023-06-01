<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { EthereumClient, w3mConnectors, w3mProvider } from '@web3modal/ethereum'
import { Web3Modal } from '@web3modal/html'
// import { Web3Modal } from '@web3modal/core'
import { configureChains, createConfig } from '@wagmi/core'
import { arbitrum, mainnet, polygon } from '@wagmi/core/chains'
import { getAccount, getNetwork } from '@wagmi/core'

defineProps<{ msg: string }>()
const chains = [arbitrum, mainnet, polygon]
const projectId = 'd1fb5ad7ec6097f12d41cf20db86054c'

const { publicClient } = configureChains(chains, [w3mProvider({ projectId })])
const wagmiConfig = createConfig({
  autoConnect: true,
  connectors: w3mConnectors({ projectId, version: 2, chains }),
  publicClient
})
const ethereumClient = new EthereumClient(wagmiConfig, chains)
const web3modal = new Web3Modal({ projectId }, ethereumClient)

let account = ref()
let network = ref()

onMounted( async() => {
  console.log("web3modal", web3modal)
})

const handleClickGetAccount = async () => {
  const accountObj = getAccount()
  account.value = accountObj.address
}
const handleClickGetNetwork = async () => {
  const networkObj = getNetwork()
  network.value = networkObj.chain?.id
}
</script>

<template>
  <h1>{{ msg }}</h1>
  <div class="card">
    <w3m-core-button></w3m-core-button>&nbsp;
    <w3m-network-switch></w3m-network-switch>
  </div>
  <div class="card">
    <div>{{ account }}</div>
    <button type="button" @click="handleClickGetAccount">get wallet address</button>
  </div>
  <div class="card">
    <div>{{ network }}</div>
    <button type="button" @click="handleClickGetNetwork">get wallet network</button>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
button {
  border: 1px solid #0b9660;
}
</style>
