
<script setup>
import { ref } from "vue";
import axios from "axios";
import CityCard from "./CityCard.vue";
import { useRoute, useRouter } from "vue-router";

const router =useRouter();
const goToCityView = (city) => {
    router.push({
    name: "CityView",
    params: { country: city.country, state: city.state, city: city.city },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
    }
const savedCities = ref([]);
const getCities = async() => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${
            city.coords.lat
          }&lon=${city.coords.lng}&appid=${
            import.meta.env.VITE_OPEN_KEY
          }&units=metric`
        )
      );
    });
    const weatherData=await Promise.all(requests);
    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
        
    })
  }
};
await getCities(); 
</script>


<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"/>
    </div>
    <p class="text-center font-mono text-xl" v-if="savedCities.length === 0"> No locations added. <br>
     To start tracking a location, search in
    the field above.</p>
</template>


