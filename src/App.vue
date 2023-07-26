<script setup>
import { ref, inject, computed } from "vue";
import useSWRV from "swrv";

const axios = inject("axios");

const apiBase = "https://open.er-api.com/v6/latest/MYR";

const getList = () => {
  console.log("fetching data...");
  return axios.get(apiBase).then((response) => response.data);
};

const countryCode = computed(() => {
  return currencies.value;
});

const {
  data: currencies,
  error: currenciesError,
  mutate: mutateCurrencies,
} = useSWRV(apiBase, getList);
</script>

<template>
  <div>
    <!-- <div>{{ countryCode }}</div> -->
    <p>Currency Converter</p>

<div v-if="countryCode">
    <select
      id="currencyCode"
      class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
    >
      <option v-for="(currency,index) in countryCode.rates" :key="index">
        {{ currency }}
      </option>
    </select>
</div>


    <button
      class="text-gray-900 bg-gradient-to-r from-teal-200 to-lime-200 hover:bg-gradient-to-l hover:from-teal-200 hover:to-lime-200 focus:ring-4 focus:outline-none focus:ring-lime-200 dark:focus:ring-teal-700 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2"
      @click="getList()"
    >
      Call
    </button>
  </div>
</template>

<style scoped>
</style>
