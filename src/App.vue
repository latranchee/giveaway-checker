<script setup>
import  {ref, onMounted} from 'vue'
import {Account, Chains, Validator } from "./utils/Client"
import HeadingRow from "./components/HeadingRow.vue"
let address = ref("");
let chains = ref([]);
let eligible = ref(false);
let loading = ref(false);
let error = ref(false);
let submitted = ref(false);
let double = ref(false);
let cosmosValidator = ref({});
let junoValidator = ref({});

let validators = ref([
    {
      name: "Juno",
      validator: "junovaloper1uepjmgfuk6rnd0djsglu88w7d0t49lml7kqufu",
      commission: "5%",
      chain: "juno",
      restake: true
    },
    {
      name: "Cosmos",
      validator: "cosmosvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmljdpae2",
      commission: "5%",
      chain: "cosmoshub",
      restake: true
    },
    {
      name: "Akash",
      validator: "akashvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmlsqkfuf",
      commission: "5%",
      chain: "akash",
      restake: true
    }
    ,{
      name: "Evmos",
      validator: "evmosvaloper1pz3mcahcrglf3md4lggax5r95gvmppc6x5w7hw",
      commission: "5%",
      chain: "evmos",
      restake: false
    },
    {
      name: "Sifchain",
      validator: "sifvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmlmxj56z",
      commission: "5%",
      chain: "sifchain",
      restake: false
    },
    {
      name: "Lum Network",
      validator: "lumvaloper1kn7zgwex5yr897mp9vy83vm9re53skyhr82s58",
      commission: "10%",
      chain: "lumnetwork",
      restake: true

    },
    {
      name: "Chihuahua",
      validator: "chihuahuavaloper1zl4vt84hya03e8hu7dx4q4cvn2ts2xdr685p5g",
      commission: "5%",
      chain: "chihuahua",
      restake: false
    },
    {
      name: "Cerberus",
      validator: "cerberusvaloper1zl4vt84hya03e8hu7dx4q4cvn2ts2xdrrnnufr",
      chain: "cerberus",
      restake: true,
      commission: "5%"
    },
    {
      name: "Nomic",
      validator: "nomicvaloper",
      commission: "5%",
      chain: "nomic",
      isNomic: true,
      restake: false
    }
])


onMounted(async ()=> {
let chainFetcher = new Chains(["juno", "cosmoshub"]);
await chainFetcher.init();
chains.value = chainFetcher.chains;
const validatorFetcher  = new Validator(chains.value);
await validatorFetcher.init();
junoValidator.value =  await validatorFetcher.getJunoValidator();
cosmosValidator.value = await validatorFetcher.getCosmosValidator();


})


let handleSubmit = async (e) => {
  submitted.value = true;
  loading.value = true;
 let account = new Account(address.value, chains.value);
 let hasError = await account.init();
 if (hasError ) {
   error.value = true;
 }
  eligible.value = account.eligible;
  double.value = account.double;
 loading.value= false;

}

let reset = () => {
  address.value = "";
  eligible.value = false;
  double.value = false;
  submitted.value = false;
  error.value = false;
  loading.value = false;
}

let getKeplr = async () => {
  window.keplr.enable("juno-1");
  const key = await window.keplr.getKey("juno-1");
  address.value =key.bech32Address;
}

</script>

<template>
<v-app class="app" full-heigth >
  <v-container>
  <HeadingRow />
  <v-row v-if="!submitted">
    <v-col>
      <v-card class="checker-form mt-15">
        <v-card-title>
          <div class="mt-5">Verify your eligibility</div>
        </v-card-title>
        <v-card-text>
          <v-btn flat class="keplr-button mb-5" color="#5e72e4" @click="getKeplr">Get Address From Keplr</v-btn>
          <div class="text-body-2 mb-3"> OR</div>
        <v-form @submit.prevent="handleSubmit">
          <v-text-field type="text" v-model="address" label="Enter your address" > </v-text-field>
          <v-btn flat color="primary" type="submit"> Check</v-btn>
        </v-form>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
  <v-row  v-if="submitted"> 
    <v-col>
      <v-card class="checker-form">
        <v-card-title>Eligibility</v-card-title>
        <v-card-text>
          <v-progress-circular indeterminate v-if="loading && !error"></v-progress-circular>
          <v-alert v-if="error" type="error">An error occured with the servers. Please try again.</v-alert>
          <div v-if="!loading && !error">
            <div v-if="eligible && !double">
              <div class="text-body-1">You are eligible for the 50 $JUNO giveaway </div>
               <div class="text-body-1 my-5">Would you love to get 100 $JUNO instead of 50?</div>
              <div class="text-body-2">Delegate 5 ATOM to Stake Frites </div>
              <div class="mt-5">
                <v-btn href="https://wallet.keplr.app/#/cosmoshub/stake?modal=stake&validator=cosmosvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmljdpae2">Delegate on KEPLR</v-btn>
                <v-btn flat color="primary" class="ml-3" href="https://restake.app/cosmoshub/cosmosvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmljdpae2">Delegate on Restake</v-btn>
              </div>
            </div>
            <div v-if="eligible && double">
              <div class="text-body-1">You are eligible for the 100 $JUNO giveaway</div>
            </div>
            <div v-if="!eligible && !double">
              <div class="text-body-1">You not eligible. <br> You need to delegate at least 5 $JUNO to Stake Frites 🥩 🍟  </div>
              <div class="mt-5">
                <v-btn href="https://wallet.keplr.app/#/juno/stake?chainId=juno-1&modal=stake&validator=junovaloper1uepjmgfuk6rnd0djsglu88w7d0t49lml7kqufu">Delegate on KEPLR</v-btn>
                <v-btn flat color="primary" class="ml-3" href="https://restake.app/juno/junovaloper1uepjmgfuk6rnd0djsglu88w7d0t49lml7kqufu">Delegate on Restake</v-btn>
              </div>
              <div class="text-body-1 my-5">Would you love to get 100 $JUNO instead of 50?</div>
              <div class="text-body-2">Delegate 5 ATOM to Stake Frites </div>
              <div class="mt-5">
                <v-btn href="https://wallet.keplr.app/#/cosmoshub/stake?modal=stake&validator=cosmosvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmljdpae2">Delegate on KEPLR</v-btn>
                <v-btn flat color="primary" class="ml-3" href="https://restake.app/cosmoshub/cosmosvaloper1uepjmgfuk6rnd0djsglu88w7d0t49lmljdpae2">Delegate on Restake</v-btn>
              </div>
            </div>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-btn @click="reset">Check again</v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
  <v-row>
    <v-col>
      <v-card class="checker-form mt-10"> 
        <v-card-title>How to support us?</v-card-title>
          <v-list>
            <v-list-subheader class="mb-5">
                At Stake Frites, we are huge believers of decentralization.<br>
                We are also a bunch of entrepreneurs, and developpers trying to give our all to the community. <br>
                Consider delegating with us on KEPLR or Restake to help us to grow.
            </v-list-subheader>
            <v-list-item target="__blank__" lines="three" v-for="validator in validators" :href=" validator.isNomic ? `https://app.nomic.io/` : validator.restake ? `https://restake.app/${validator.chain}/${validator.validator}` : `https://wallet.keplr.app/#/${validator.chain}/stake?modal=stake&validator=${validator.validator}`">
              <v-list-item-header>
                <v-list-item-title><div class="text-h6">{{validator.name}}</div></v-list-item-title>
                <v-list-item-subtitle><strong>Commission:</strong> {{validator.commission}}</v-list-item-subtitle>
              </v-list-item-header>
            </v-list-item>
          </v-list>
      </v-card>
    </v-col>
  </v-row>

</v-container>
</v-app>
</template>

<style>


.app {
  height: 100vh;
  overflow-y: scroll;
}
.keplr-button {
  color: white!important;
  font-weight: bold !important;
}

.checker-form {
  margin: 0 auto;
  /* max-width: 800px; */
}
</style>