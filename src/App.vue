<script setup>
import { unref, ref } from "vue";
import { dinero, toFormat, toSnapshot } from "dinero.js";
import { USD, KHR } from "@dinero.js/currencies";
import { computed } from "@vue/reactivity";

const currencies = [USD, KHR];
const CURRENCIES_MAP = Object.fromEntries(
  currencies.map((item) => [item.code, item])
);

// refs
const amount = ref(0);
const scale = ref(2);
const currency = ref(USD.code);

// computed
const selectedCurrency = computed(() => CURRENCIES_MAP[currency.value]);

// add decimal place
const normalizedAmount = computed(() => Math.floor(amount.value * 100));

const encodedAmount = computed(() =>
  dinero({
    amount: unref(normalizedAmount),
    currency: selectedCurrency.value,
    scale: scale.value,
  })
);

const snapshot = computed(() => JSON.stringify(toSnapshot(encodedAmount.value), null, 2));
const formattedAmount = computed(() =>toFormat(encodedAmount.value, (v) => `${v.currency.code} ${v.amount}`));
</script>

<template>
  <div>
    <h1>@dinero/playground</h1>

    <label for="amount">Amount in Dollar/KHR</label>
    <input
      name="amount"
      id="amount"
      type="number"
      v-model="amount"
      placeholder="amount"
    />

    <label for="currency">Currency</label>   
    <select v-model="currency" id="currency" name="currency">
      <option v-for="item in currencies" :value="item.code" :key="item.code">
        {{ item.code }}
      </option>
    </select>

    <pre>{{ selectedCurrency }}</pre>

    <label for="scale">Scale</label>
    <input
      name="scale"
      id="scale"
      type="number"
      v-model="scale"
      placeholder="scale"
    />
    <h4>
      Formatted value: <strong>{{ formattedAmount }}</strong>
    </h4>
    <pre>{{ snapshot }}</pre>
    <hr>
    <a target="_blank" href="https://github.com/seanghay/dinero-playground"><small>View on GitHub</small></a>
  </div>
</template>

<style>
#app {
  max-width: 512px;
  padding: 2em;
  margin: 0 auto;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
</style>
