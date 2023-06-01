<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { ethers } from 'ethers';
import { EthereumProvider } from "@walletconnect/ethereum-provider";
defineProps<{ msg: string }>()

const projectId = '496c33146f0305590d925fe8cecab521'

let provider:any
let account = ref()
let network = ref()

onMounted( async() => {
  provider = await EthereumProvider.init({
      projectId,
      showQrModal: true,
      qrModalOptions: { themeMode: "light" },
      chains: [137, 1, 56],
      methods: ["eth_sendTransaction", "personal_sign"],
      events: ["chainChanged", "accountsChanged", "disconnect", "connect", "session_event", "display_uri"],
      metadata: {
        name: "Dev Dapp",
        description: "My Dapp description",
        url: "https://my-dapp.com",
        icons: ["https://my-dapp.com/logo.png"],
      },
    });
    if(provider.accounts[0]){
      account.value = provider.accounts[0]
    }
    console.log("provider", provider)
})

const onConnect = async () => {
  try {
    if(provider){
        let res =  await provider.enable();
        console.log("=res=",res)
        console.log("provider",provider)
        if(provider.accounts[0]){
          account.value = provider.accounts[0]
        }
        onloadListener()
    }
  } catch (err) {
    console.error(err);
  } finally {
    console.log("finally")
  }
}

const handleClickGetAccount = async () => {
  const ethersProvider = new ethers.BrowserProvider(provider);
  let res = await ethersProvider.listAccounts()
  let Network = await ethersProvider.getNetwork()
  console.log("ethersProvider", res[0].address)
  console.log("Network", Network.name)
  network.value = Network.name
}

const onDisconnect = async () => {
  provider.disconnect().then(() => {
    console.log("Disconnected");
    console.log("provider",provider)
  }).catch((error:any) => {
    console.error("Disconnect error:", error);
  });
}


const  onloadListener = async () => {

  provider.on('chainChanged', (chainId:number) => {
    console.log('网络发生了变化，新的链ID为:', chainId);
  });
  
  provider.on("accountsChanged", (accountsChanged: string)=>{
    console.log("onLoad accountsChanged")
    console.log("accountsChanged",accountsChanged)
  });
  provider.on("connect", (connect:string)=>{
    console.log("onLoad connect")
    console.log("connect",connect)
  });
  provider.on("session_event", (session_event:object)=>{
    console.log("onLoad session_event")
    console.log("session_event",session_event)
  });
  provider.on("display_uri", (display_uri:any)=>{
    console.log("onLoad display_uri")
    console.log("display_uri",display_uri)
  });
  provider.on("disconnect", (disconnect:any)=>{
    console.log("onLoad disconnect")
    console.log("disconnect",disconnect)
  });
}
</script>

<template>
  <h1>{{ msg }}</h1>
  <div class="card" v-show="!account">
    <button type="button" @click="onConnect">Connect</button>
  </div>
  <div class="card" v-show="account">
    <div>{{ account }}</div>
    <button type="button" @click="onDisconnect">Disconnect</button>
  </div>
  <div class="card">
    <div>{{ network }}</div>
    <button type="button" @click="handleClickGetAccount">get network</button>
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
