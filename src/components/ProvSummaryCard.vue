<script setup>
const props = defineProps([
  "province",
  "activeCases",
  "activeCasesDelta",
  "totalCases",
  "totalVaccinated",
  "recentDeaths",
  "recentRecovered",
  "image",
]);

// Show a green number if the cases are decreasing. Red if increasing or the same.
const deltaStyle =
  Number.parseInt(props.activeCasesDelta) <= 0
    ? "text-green-600"
    : "text-red-600";
</script>

<template>
  <div class="container border pb-3">
    <h1 class="text-2xl font-bold relative py-2">
      {{ province }}
      <img class="absolute right-3 top-2 h-7 w-auto" :src="image" />
    </h1>
    <ul>
      <li>
        <span class="font-bold">{{ activeCases }}</span> Active Cases
        <span class="text-sm tracking-tight">
          (
          <span :class="deltaStyle">{{ activeCasesDelta }}</span> from prev.
          period)
        </span>
      </li>
      <li>
        <span class="font-bold">{{ totalCases }}</span> Total cases
        <span class="text-sm tracking-tight">(cumulative)</span>
      </li>
      <li>
        <span class="font-bold">{{ totalVaccinated }}</span> Vaccinated
        <span class="text-sm tracking-tight">(cumulative)</span>
      </li>
      <li>
        <span class="font-bold">{{ recentDeaths }}</span> Deaths This Period
      </li>
      <li>
        <span class="font-bold">{{ recentRecovered }}</span> Recovered This
        Period
      </li>
    </ul>
  </div>
</template>