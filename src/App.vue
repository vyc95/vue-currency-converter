<script setup>
import { ref, inject, computed } from "vue";
import useSWRV from "swrv";

const axios = inject("axios");

const amountInput = ref(0);

const rate = ref(0);

const apiBase = "https://open.er-api.com/v6/latest";

const amountOutput = computed(() => {
  return amountInput.value * rate.value;
});

const getList = () => {
  console.log("fetching data...");
  return axios.get(apiBase).then((response) => response.data);
};

const countryCode = computed(() => {
  return currencies.value;
});

const countryCodeTo = ref(null);

const {
  data: currencies,
  error: currenciesError,
  mutate: mutateCurrencies,
} = useSWRV(apiBase, getList);

function onChangeBaseCode(event) {
  axios.get(apiBase + "/" + event.target.value).then((response) => {
    countryCodeTo.value = response.data.rates;
  });
}

function onChange(event) {
  rate.value = event.target.value;
}
</script>

<template>
  <div
    class="p-4 bg-white dark:bg-gray-800 rounded-lg shadow-md mx-auto md:w-1/2 w-full"
  >
    <h2 class="text-2xl font-semibold mb-4">Currency Converter</h2>
    <div v-if="countryCode" class="flex flex-col space-y-4">
      <input
        v-model="amountInput"
        type="number"
        placeholder="Enter amount"
        class="py-2 px-4 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
      />
      <div class="flex space-x-4">
        <div class="flex flex-col w-full">
          <label class="text-sm font-semibold" for="fromCurrency">From</label>
          <select
            @change="onChangeBaseCode($event)"
            id="fromCurrency"
            class="block w-full py-2 px-4 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
          >
            <option
              v-for="(rate, currencyCode) in countryCode.rates"
              :key="currencyCode"
              :value="currencyCode"
            >
              {{ currencyCode }}
            </option>
          </select>
        </div>
        <div class="flex flex-col w-full">
          <label class="text-sm font-semibold" for="toCurrency">To</label>
          <select
            @change="onChange($event)"
            id="toCurrency"
            class="block w-full py-2 px-4 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
          >
            <option
              v-for="(rate, currencyCode) in countryCodeTo"
              :key="currencyCode"
              :value="rate"
            >
              {{ currencyCode }}
            </option>
          </select>
        </div>
      </div>
      <button
        class="bg-gradient-to-r from-teal-200 to-lime-200 hover:bg-gradient-to-l hover:from-teal-200 hover:to-lime-200 focus:ring-2 focus:ring-lime-200 font-medium rounded-lg text-sm py-2 px-4"
        @click="getList"
      >
        Convert
      </button>
    </div>
    <p class="mt-4" v-if="amountOutput">
      {{ amountOutput }} {{ selectedToCurrency }}
    </p>
  </div>
</template>

<style scoped>
</style>
