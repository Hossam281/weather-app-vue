<template>
  <header
    class="sticky top-1 rounded-full bg-[#222831] w-[90%] lg:w-[60%] shadow-lg"
  >
    <nav
      class="flex flex-col items-center sm:flex-row gap-4 h-full text-[#EEEEEE] py-6 md:ml-10"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="group flex items-center gap-4">
          <i
            class="fa-solid fa-cloud-moon-rain cursor-pointer transition-all group-hover:text-[#00ADB5] group-hover:scale-125 duration-300 text-2xl text-[#EEEEEE]"
          ></i>
          <p class="text-2xl pb-1 duration-300 group-hover:text-[#00ADB5]">
            Weatherify
          </p>
        </div>
      </RouterLink>

      <div class="flex gap-3 h-full flex-1 justify-end">
        <i
          class="fa-solid fa-plus text-xl mr-5 cursor-pointer transition-all hover:scale-125 duration-300 mt-[-5px] hover:text-[#00ADB5] text-[#EEEEEE]"
          @click="addCity"
          v-if="route.query.preview"
        ></i>

        <i
          @click="toggleModal"
          class="fa-solid fa-circle-info text-xl mr-5 cursor-pointer transition-all hover:scale-125 duration-300 mt-[-5px] hover:text-[#00ADB5] text-[#EEEEEE]"
        ></i>
      </div>
    </nav>
  </header>
  <Modal @close="toggleModal" :modalActive="modalActive">
    <div class="text-black font-sans">
      <h1 class="text-2xl mb-1">About:</h1>
      <p class="mb-4">
        The Local Weather allows you to track the current and future weather of
        cities of your choosing.
      </p>
      <h2 class="text-2xl">How it works:</h2>
      <ol class="list-decimal list-inside mb-4">
        <li>Search for your city by entering the name into the search bar.</li>
        <li>
          Select a city within the results, this will take you to the current
          weather for your selection.
        </li>
        <li>
          Track the city by clicking on the "+" icon in the top right. This will
          save the city to view at a later time on the home page.
        </li>
      </ol>

      <h2 class="text-2xl">Removing a city</h2>
      <p>
        If you no longer wish to track a city, simply select the city within the
        home page. At the bottom of the page, there will be am option to delete
        the city.
      </p>
    </div>
  </Modal>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from "vue-router";
import { ref } from "vue";
import Modal from "./Modal.vue";

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }

  const locationObj = {
    id: crypto.randomUUID(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));

  
  
  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};

const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>
