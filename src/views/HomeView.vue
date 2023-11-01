<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";

const router = useRouter();
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapSearchResults = ref(null);
const searchError = ref(null);
const getSearchResult = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${
            searchQuery.value
          }.json?access_token=${import.meta.env.VITE_MAPBOX_KEY}&types=place`
        );
        mapSearchResults.value = result.data.features;

        return;
      } catch {
        searchError.value = true;
      }
    }
    mapSearchResults.value = null;
  }, 300);
};
const previewCity = (searchResult) => {
  const [state, city, country] = searchResult.place_name.split(",");
  router.push({ 
  name: "CityView",
  params: { state: state?.replaceAll(" ", ""), city:city?.replaceAll(" ", "") },
  query: { lat: searchResult.geometry.coordinates[1], lng: searchResult.geometry.coordinates[0] ,preview:true },

 });
};
</script>

<template>
  <main class="text-[#EEEEEE] p-[2rem] mx-auto font-sans w-[90%] lg:w-[60%]">
    <div class="pt-4 mb-8 relative">
      <input
        @input="getSearchResult"
        v-model="searchQuery"
        type="text"
        class="focus:outline-none focus:border-[#00ADB5] py-2 px-1 w-full bg-transparent border-b"
        placeholder="Search for a city"
      />

      <ul
        class="abolute bg-[#222831] text[#EEEEEE] w-full shadow-md py-2 px-2 top-[66px]"
        v-if="mapSearchResults"
      >
        <div
          v-if="searchError"
          class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
          role="alert"
        >
          <strong class="font-bold">Sorry&#128561;, </strong>
          <span class="block sm:inline"
            >Something went wrong, please try again.</span
          >
        </div>
        <p v-if="!searchError && mapSearchResults.length === 0">
          No results matched your search
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer hover:bg-[#393E46]"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList/>
        <template #fallback>
        <div class="lds-roller mt-10 mx-auto">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>
      </template>
      </Suspense>
    </div>
  </main>
</template>


<style scoped>
.lds-roller {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.lds-roller div {
  animation: lds-roller 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  transform-origin: 40px 40px;
}
.lds-roller div:after {
  content: " ";
  display: block;
  position: absolute;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #fff;
  margin: -4px 0 0 -4px;
}
.lds-roller div:nth-child(1) {
  animation-delay: -0.036s;
}
.lds-roller div:nth-child(1):after {
  top: 63px;
  left: 63px;
}
.lds-roller div:nth-child(2) {
  animation-delay: -0.072s;
}
.lds-roller div:nth-child(2):after {
  top: 68px;
  left: 56px;
}
.lds-roller div:nth-child(3) {
  animation-delay: -0.108s;
}
.lds-roller div:nth-child(3):after {
  top: 71px;
  left: 48px;
}
.lds-roller div:nth-child(4) {
  animation-delay: -0.144s;
}
.lds-roller div:nth-child(4):after {
  top: 72px;
  left: 40px;
}
.lds-roller div:nth-child(5) {
  animation-delay: -0.18s;
}
.lds-roller div:nth-child(5):after {
  top: 71px;
  left: 32px;
}
.lds-roller div:nth-child(6) {
  animation-delay: -0.216s;
}
.lds-roller div:nth-child(6):after {
  top: 68px;
  left: 24px;
}
.lds-roller div:nth-child(7) {
  animation-delay: -0.252s;
}
.lds-roller div:nth-child(7):after {
  top: 63px;
  left: 17px;
}
.lds-roller div:nth-child(8) {
  animation-delay: -0.288s;
}
.lds-roller div:nth-child(8):after {
  top: 56px;
  left: 12px;
}
@keyframes lds-roller {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

</style>
