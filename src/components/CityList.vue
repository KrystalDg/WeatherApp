<template>
  <div v-for="city in savedCities" :key="city.id">
    <city-card :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import CityCard from "./CityCard.vue";

const savedCities = ref([]);
const router = useRouter();

const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
  const request = [];
  savedCities.value.forEach((city) => {
    const cityData = axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=7efa332cf48aeb9d2d391a51027f1a71&&units=metric`
    );
    request.push(cityData);
  });

  const weatherData = await Promise.all(request);

  //Flicker Delay
  await new Promise((res) => {
    setTimeout(res, 1000);
  });

  weatherData.forEach((value, index) => {
    savedCities.value[index].weather = value.data;
  });
};

await getCities();

const goToCityView = (city) => {
  router.push({
    name: "city-view",
    params: {
      state: city.state,
      city: city.city,
    },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lon: city.coords.lon,
    },
  });
};
</script>
