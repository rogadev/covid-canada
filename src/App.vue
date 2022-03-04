<script setup>
import ProvSummaryCard from "./components/ProvSummaryCard.vue";

import { ref } from "vue";
const summary = ref("");
const refreshDate = ref("");
getData();

/**
 * Handles getting the data, either from local storage or from the API.
 * @returns {Object}  The data object.
 */
function getData() {
  let data = getDataFromLocalStorage();
  if (requiresRefresh() || data === null) {
    fetchDataFromApi();
  } else {
    summary.value = data.summary;
  }
}

/**
 * Attempts to get data from local storage
 * @return {String | null} - JSON string of data or null if not found
 */
function getDataFromLocalStorage() {
  console.log("Attempting to retireve data from local storage...");
  let data = localStorage.getItem("data");
  if (data === null) {
    console.log("No data found in local storage.");
    return null;
  }
  return JSON.parse(data);
}

/**
 * Fetch fresh data from our API
 * @returns {String} JSON string of data from our API
 */
async function fetchDataFromApi() {
  console.log("Attempting to fetch data from the API...");
  let response = await fetch("https://api.opencovid.ca/summary"); // Returns a Response object
  let result = await response.json(); // Parses res.body into JS object
  localStorage.setItem("data", JSON.stringify(result)); // Stringify JS object into JSON
  refreshDate.value = new Date().toISOString().split("T")[0];
  localStorage.setItem("lastUpdated", refreshDate.value);
  summary.value = result.summary;
}

/**
 * Check if the data is stale.
 * @returns {boolean} True if the data is stale.
 */
function requiresRefresh() {
  let lastUpdated = localStorage.getItem("lastUpdated")?.split("T")[0]; // Gives us YYYY-MM-DD
  let now = new Date().toISOString().split("T")[0]; // Gives us YYYY-MM-DD
  console.log(lastUpdated, now);
  if (lastUpdated === now) {
    console.log("LocalStorage requires refresh.");
    refreshDate.value = lastUpdated;
    return false;
  }
  refreshDate.value = now;
  return true;
}

/**
 * Provide the appropriate asset path for province image source.
 * @param {String} prov Province name
 */
function getProvImage(prov) {
  switch (prov) {
    case "Alberta":
      return "/img/alberta.jpg";
    case "BC":
      return "/img/bc.jpg";
    case "Manitoba":
      return "/img/manitoba.jpg";
    case "New Brunswick":
      return "/img/new_brunswick.jpg";
    case "NL":
      return "/img/newfoundland.jpg";
    case "Nova Scotia":
      return "/img/nova_scotia.jpg";
    case "Ontario":
      return "/img/ontario.jpg";
    case "PEI":
      return "/img/pei.jpg";
    case "Quebec":
      return "/img/quebec.jpg";
    case "Saskatchewan":
      return "/img/sask.jpg";
    case "Yukon":
      return "/img/yukon.jpg";
    case "Nunavut":
      return "/img/nunavut.jpg";
    case "NWT":
      return "/img/nwt.jpg";
    default:
      return "/img/canada.svg";
  }
}

/**
 * Use the Intl.NumberFormat API to format numbers.
 * @param {String | Number} num Input number or string to be formatted. Strings will be parsed to numbers.
 * @returns {String} Formatted number.
 */
function formatNumber(num) {
  num = +num;
  num = Intl.NumberFormat("en-CA").format(Number.parseInt(num));
  if (num === "NaN") num = "N/A"; // Repatriated has a N/A value
  return num;
}
</script>

<template>
  <h1 class="text-4xl font-extrabold">COVID-19 Data</h1>
  <h2 class="text-3xl font-bold">
    Canada: <time :datetime="refreshDate">{{ refreshDate }}</time>
  </h2>
  <div class="mt-8 mx-6 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <ProvSummaryCard
      v-for="s in summary"
      :key="s.province"
      :province="s.province"
      :image="getProvImage(s.province)"
      :activeCases="formatNumber(s.active_cases)"
      :activeCasesDelta="formatNumber(s.active_cases_change)"
      :totalCases="formatNumber(s.cumulative_cases)"
      :totalVaccinated="formatNumber(s.cumulative_cvaccine)"
      :recentDeaths="formatNumber(s.deaths)"
      :recentRecovered="formatNumber(s.recovered)"
    />
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
