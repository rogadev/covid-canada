<script setup>
import { ref } from "vue";

const requiresAbbr = ref(false);
const abbr = ref("");

const props = defineProps([
  "province",
  "activeCases",
  "activeCasesDelta",
  "recentDeaths",
  "image",
]);

switch (props.province) {
  case "BC":
    requiresAbbr.value = true;
    abbr.value = "British Columbia";
    break;
  case "NL":
    requiresAbbr.value = true;
    abbr.value = "Newfoundland and Labrador";
    break;
  case "PEI":
    requiresAbbr.value = true;
    abbr.value = "Prince Edward Island";
    break;
  case "NWT":
    requiresAbbr.value = true;
    abbr.value = "Northwest Territories";
    break;
  case "Repatriated":
    requiresAbbr.value = true;
    abbr.value =
      "Citiziens of Canada who have been repatriated to Canada from other countries";
    break;
  default:
    requiresAbbr.value = false;
    abbr.value = "";
}

// Show a green number if the cases are decreasing. Red if increasing or the same.
const deltaStyle =
  Number.parseInt(props.activeCasesDelta) <= 0
    ? "text-green-600"
    : "text-red-600";
</script>

<template>
  <div
    class="container border pb-3 rounded-md shadow-md cursor-pointer"
    @click="$emit('openModal', province, image)"
  >
    <h1
      class="
        text-2xl
        font-bold
        relative
        py-2
        flex
        justify-between
        align-middle
        items-center
        px-6
      "
    >
      <img
        class="h-auto w-5"
        src="/svg/same.svg"
        v-if="Number.parseInt(activeCasesDelta) === 0"
      />
      <img
        class="h-auto w-5"
        src="/svg/up.svg"
        v-else-if="Number.parseInt(activeCasesDelta) > 0"
      />
      <img class="h-auto w-5" src="/svg/down.svg" v-else />
      <abbr :title="abbr" class="cursor-pointer" v-if="requiresAbbr">
        {{ province }}
      </abbr>
      <span class="cursor-default" v-else>{{ province }}</span>
      <img class="h-auto w-5" :src="image" />
    </h1>
    <ul>
      <li>
        <span class="font-bold">{{ activeCases }}</span> Active Cases
        <span class="text-sm tracking-tight">
          (<span :class="deltaStyle">{{ activeCasesDelta }}</span> from prev.
          period)
        </span>
      </li>
      <li>
        <span class="font-bold">{{ recentDeaths }}</span> Deaths This Period
      </li>
    </ul>
  </div>
</template>
