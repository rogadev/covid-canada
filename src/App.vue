<script setup>
import ProvSummaryCard from "./components/ProvSummaryCard.vue";
import DetailsModal from "./components/DetailsModal.vue";

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
  let sortedData = result.summary.sort((a, b) => {
    return b.active_cases - a.active_cases;
  });
  summary.value = sortedData;
  localStorage.setItem("data", JSON.stringify(result)); // Stringify JS object into JSON
  refreshDate.value = new Date().toISOString().split("T")[0];
  localStorage.setItem("lastUpdated", refreshDate.value);
}

/**
 * Check if the data is stale.
 * @returns {boolean} True if the data is stale.
 */
function requiresRefresh() {
  let lastUpdated = localStorage.getItem("lastUpdated")?.split("T")[0]; // Gives us YYYY-MM-DD
  let now = new Date().toISOString().split("T")[0]; // Gives us YYYY-MM-DD
  if (lastUpdated === now) {
    console.log("LocalStorage requires refresh.");
    refreshDate.value = formatDate(lastUpdated);
    return false;
  }
  refreshDate.value = formatDate(now);
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

/**
 * Format the date using the Intl DateTimeFormat API.
 * @param {Date | String} date Date object or string
 */
function formatDate(date) {
  const options = {
    year: "numeric",
    month: "long",
    day: "numeric",
  };
  return new Intl.DateTimeFormat("en-CA", options).format(new Date(date));
}

let showModal = ref(false);
let modalProvince = ref("");
let modalImage = ref("");

function openModal(province, image) {
  showModal.value = true;
  modalProvince.value = province;
  modalImage.value = image;
}

function closeModal() {
  showModal.value = false;
}
</script>

<template>
  <h1 class="text-4xl font-extrabold">Canada COVID-19 Data</h1>
  <h2 class="text-3xl font-bold">
    Last Updated <time :datetime="refreshDate">{{ refreshDate }}</time>
  </h2>
  <p class="pt-4 w-1/2 mx-auto font-bold text-red-500">
    NOTE: Due to a recent upgrade with the opencovid.ca API, we are working
    dilligently to update the app to accomidate new changes. If stats are not
    display below, please check back tomorrow.
  </p>
  <div
    class="
      mt-8
      mx-auto
      grid grid-cols-1
      md:grid-cols-2
      lg:grid-cols-3
      gap-4
      mb-7
      w-4/5
    "
  >
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
      @openModal="openModal"
    />
  </div>
  <DetailsModal
    v-if="showModal"
    :province="modalProvince"
    :image="modalImage"
    :details="summary.find((s) => s.province === modalProvince)"
    @closeModal="closeModal"
  />
  <footer>
    <small class="pb-5"
      >&copy; 2022 Ryan Paranich |
      <a
        class="text-blue-700 underline"
        href="https://www.roga.dev"
        target="blank"
        >Roga.dev</a
      ></small
    >
    <br />
    <small class="pb-5"
      >Data provided by
      <a
        class="text-blue-700 underline"
        href="https://opencovid.ca"
        target="blank"
        >COVID-19 Canada Open Data Working Group</a
      >
    </small>
  </footer>
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
