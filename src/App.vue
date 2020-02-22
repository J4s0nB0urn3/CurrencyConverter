<template>
  <div id="app" class="converter-outer">
    <div class="converter-inner">
      <h1>Currency Converter</h1>
      <p>Rate Date: {{this.date}}</p>
      <p>Get historical rates for any day since 1999:</p>
      <input class="input" autocomplete="off" v-model="yourChoiceOfDate" type="date" min="1999-01-04">
      <button v-if="yourChoiceOfDate" v-on:click="fetchSpecialDate(); resetData()">Go!</button><br><br>
      <form v-on:submit.prevent class="form">
        <fieldset>
        <label id="currency">From (Please choose): </label>
        <select class="input" autocomplete="off" v-model="fromCurrency" required>
          <option value="">Select Currency</option>
          <option v-for="(value, key) in currencies" :value="key">{{ key }}</option>
        </select>
      </fieldset>
      <fieldset>
        <label id="currency">To (Please choose): </label>
        <select class="input" autocomplete="off" v-model="intoCurrency" required>
          <option value="">Select Currency</option>
          <option v-for="(value, key) in currencies" :value="key">{{ key }}</option>
        </select>
      </fieldset>
        <label id="currency">Amount: </label>
        <input class="input" v-model="selectedAmount" type="number" step="0.001" required><br>
        <button v-on:click="changeCurrency(); oneByOne()">Convert</button>
        <button type="reset" v-on:click="resetResult">Reset</button>
      </form>
      <p v-if="result>0">Result: {{result}} {{intoCurrency}}</p>
      <p v-if="result>0">{{crossResult1}}</p>
      <p v-if="result>0">{{crossResult2}}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      currencies: [],
      date: "",
      specialDate: "",
      yourChoiceOfDate: "",
      fromCurrency: "",
      selectedAmount: null,
      intoCurrency: "",
      result: null,
      oneNewCurrency: 0,
      oneOldCurrency: 0,
      crossResult1: null,
      crossResult2: null
  }
  },
  mounted() {
    this.fetchCurrencies();
  },
  methods: {
    fetchCurrencies: function() {
      const request = fetch('https://api.exchangeratesapi.io/latest')
      .then(res => res.json())
      .then(res => {
        res.rates['EUR'] = 1;
        this.currencies = res.rates;
        this.date = res.date;
      })
    },
    fetchSpecialDate: function() {
      const request = fetch(`https://api.exchangeratesapi.io/${this.yourChoiceOfDate}`)
      .then(res => res.json())
      .then(data => {
        this.currencies = data.rates; this.specialDate = data.date
      })
    },
    resetData: function() {
        this.fromCurrency= "",
        this.selectedAmount= null,
        this.intoCurrency= "",
        this.result= null,
        this.oneNewCurrency= "",
        this.oneOldCurrency= "",
        this.crossResult1= null,
        this.crossResult2= null,
        this.date = this.yourChoiceOfDate;
    },
    resetResult: function() {
      this.result= null,
      this.crossResult1= null,
      this.crossResult2= null
    },
    changeCurrency: function() {
      let euroFromOldCurrency = this.selectedAmount / this.currencies[this.fromCurrency];
      let newCurrencyFromEuro = euroFromOldCurrency * this.currencies[this.intoCurrency];
      this.result = newCurrencyFromEuro.toFixed(2);
      return this.result;
    },
    oneByOne: function() {
      let euroFromOldCurrency = this.selectedAmount / this.currencies[this.fromCurrency];
      let newCurrencyFromEuro = euroFromOldCurrency * this.currencies[this.intoCurrency];
      this.result = newCurrencyFromEuro.toFixed(2);
      this.oneOldCurrency = this.result / this.selectedAmount;
      this.oneNewCurrency = this.selectedAmount / this.result;
      this.crossResult1 = `1 ${this.fromCurrency} = ${this.oneOldCurrency.toFixed(2)} ${this.intoCurrency}`;
      this.crossResult2 = `1 ${this.intoCurrency} = ${this.oneNewCurrency.toFixed(2)} ${this.fromCurrency}`;
      return this.crossResult1, this.crossResult2;
    }
  }
}
</script>

<style>
#app {
  font-family: 'Electrolize';
  font-size: 1.4rem;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

.converter-outer {
  height: auto;
  width: 60vw;
  border: 5px solid lightgrey;
  border-style: ridge;
  text-align: center;
  position: relative;
  left: 20vw;
  right: 20vw;
  top: 5vh;
  background-color: #F0F0F0;
  box-shadow: 5px 5px 5px black;
}

.converter-inner {
  margin: 5%;
}

fieldset {
  display: block;
  border: none;
}

.input {
  font-family: 'Electrolize';
  font-size: 1rem;
}

.input:focus {
  outline: 0;
}

button {
  font-family: 'Electrolize';
  font-size: 1rem;
  margin: 10px;
  font-weight: 500;
  border-radius: 5px;
  box-shadow: 3px 3px 1px black;
  transition: 0.1s all;
}

button:focus {
  outline: 0;
}

button:active {
  box-shadow: 1px 1px 0.5px black;
  transform: translateY(3px);
}
</style>
