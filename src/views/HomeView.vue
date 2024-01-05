<template>
  <div>
    <main class="container text-white">
      <div class="pt-4 mb-8 relative">
        <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for city or state"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
        <ul v-if="openWeatherApiSearchResults"
          class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
          <p v-if="searchError">Sorry, something went wrong, please try again.</p>
          <p v-if="!searchError && openWeatherApiSearchResults.length === 0">No result match your query, try a different
            term.</p>
          <template v-else>
            <li v-for="(searchResult, i) in openWeatherApiSearchResults" :key="i" class="py-2 cursor-pointer"
              @click="previewCity(searchResult)">
              {{ searchResult.name }}, {{ searchResult.state }} {{ searchResult.country }}</li>
          </template>
        </ul>
      </div>
      <div class="flex flex-col gap-4">
        <Suspense>
          <CityList />
          <template #fallback>
            <p>Loading...</p>
          </template>
        </Suspense>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
import * as constants from '../extra/constants.js';
import CityList from '@/components/CityList.vue';

const router = useRouter();
const searchQuery = ref("");
const queryTimeout = ref(null);
const openWeatherApiSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&limit=5&appid=${constants.OPENWEATHER_API_KEY}`);
        console.log(result.data);
        openWeatherApiSearchResults.value = result.data;
      } catch {
        searchError.value = true;
      }

      return;
    }
  }, 300);
};

const previewCity = (searchResult) => {
  console.log(searchResult);
  const [city, state] = [searchResult.name, searchResult.state];
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.lat,
      lng: searchResult.lon,
      preview: true,
    }
  });
};

</script>
<style lang="scss" scoped></style>