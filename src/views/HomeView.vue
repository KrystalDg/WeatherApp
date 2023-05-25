<template>
  <main class="container text-white">
    <div class="relative pt-4 mb-8">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
        <p class="py-2" v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p class="py-2" v-if="!searchError && mapboxSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <city-list />
        <template #fallback>
          <div class="flex w-full items-center justify-center text-white">
            <p>Loading...</p>
          </div>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import CityList from "../components/CityList.vue";

const router = useRouter();

const mapboxAPIKey =
  "pk.eyJ1Ijoibmhva2xhbmd0aGFuZzI1NDciLCJhIjoiY2xoenF0dWY2MWdnMzNsbXdrcXcwdjI1eSJ9.JEa2GFhw5uTR0Hl2Nwa62w";

const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
        // console.log(mapboxSearchResults.value);

        return;
      } catch (error) {
        searchError.value = true;
      }
    }
    mapboxSearchResults.value = null;
  }, 300);
};

const previewCity = (searchResult) => {
  // console.log(searchResult);
  const [city, state] = searchResult.place_name.split(", ");
  // console.log(city, state);
  router.push({
    name: "city-view",
    params: {
      state: state,
      city: city,
    },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lon: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
</script>
