<script setup lang="ts">
import { ref } from 'vue'
import { BrowserProvider, Contract } from 'ethers'
import abi from './abi.json'

const contractAddress = '0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512'

const epdAhvSearch = ref('')
const epdBirthdateSearch = ref('')
const epdAhvInsert = ref('')
const epdBirthdateInsert = ref('')
const epdValue = ref('')
const result = ref<string | null>(null)

let provider: BrowserProvider
let signer: any
let contract: Contract

const init = async () => {
  provider = new BrowserProvider((window as any).ethereum)
  await provider.send("eth_requestAccounts", [])
  signer = await provider.getSigner()
  contract = new Contract(contractAddress, abi, signer)
}

const insertEPD = async () => {
  if (!contract) await init()
  const hash = await sha256(epdAhvInsert.value + epdBirthdateInsert.value)
  const tx = await contract.insertEPD(hash, epdValue.value)
  await tx.wait()
  alert('EPD inserted')
}

async function sha256(message: string) {
  const msgBuffer = new TextEncoder().encode(message);
  const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  const hashHex = hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
  console.log(hashHex);
  return hashHex;
}

const searchEPD = async () => {
  if (!contract) await init()
  const hash = await sha256(epdAhvSearch.value + epdBirthdateSearch.value)
  const value: string = await contract.searchEPD(hash)
  result.value = value
}
</script>

<template>
  <div class="min-h-screen flex flex-col justify-center items-center text-white px-4">

    <h1 class="text-4xl font-bold mb-12 text-center p-8">EPD Registry</h1>

    <div class="flex gap-12 max-w-full w-full">
      
      <div class="flex-1 bg-gray-800 p-6 rounded-lg shadow-md">
        <h2 class="text-xl font-semibold mb-6 pb-2">Search</h2>
        <input v-model="epdAhvSearch" placeholder="AHV" class="border px-3 py-2 w-full rounded mb-4 text-white" />
        <input v-model="epdBirthdateSearch" placeholder="Birthdate" class="border px-3 py-2 w-full rounded mb-4 text-white" />
        <button @click="searchEPD" class="bg-green-500 text-white px-4 py-2 rounded w-full mb-4">Search</button>
        <div v-if="result">
          <p class="font-mono"><strong>{{ epdAhvSearch }}</strong> → {{ result }}</p>
        </div>
      </div>

      <div class="flex-1 bg-gray-800 p-6 rounded-lg shadow-md">
        <h2 class="text-xl font-semibold mb-6 pb-2">Insert</h2>
        <input v-model="epdAhvInsert" placeholder="AHV" class="border px-3 py-2 w-full rounded mb-4 text-white" />
        <input v-model="epdBirthdateInsert" placeholder="Birthdate" class="border px-3 py-2 w-full rounded mb-4 text-white" />
        <input v-model="epdValue" placeholder="EPD Provider" class="border px-3 py-2 w-full rounded mb-4 text-white" />
        <button @click="insertEPD" class="bg-blue-500 text-white px-4 py-2 rounded w-full">Insert</button>
      </div>

    </div>
  </div>
</template>



<style scoped>
body {
  font-family: 'Inter', sans-serif;
}
</style>
