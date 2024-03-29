

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" v-model="searchQuery" @input="getSearchResults" placeholder="Search for a city or state"
        class="text-yellow-400 py-2 px-1 w-full bg-transparent border-b focus:border-red-400 focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul class="absolute bg-red-200 text-black w-full shadow-md py-2 px-1 top-[66px] rounded-xl"
        v-if="mapboxSearchResults">
        <p v-if="searchError">Sorry, something went wrong, please try again.</p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">No results match your query, please try a different
          term.</p>
        <template v-else>
          <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer"
            @click="previewCity(searchResult)">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>


<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "@/components/CityList.vue";
import CityCardSkeleton from "@/components/CityCardSkeleton.vue";

const router = useRouter();
const mapboxAPIKey = "pk.eyJ1Ijoiam9obm5pZWxwIiwiYSI6ImNscmJ6bmEwdjAweXAycWxhcnE3eHVxbzYifQ.x98SphCNINNkcpAY2htf0A";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const previewCity = (searchResult) => {
  console.log(searchResult);
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    }
  });
};

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapboxSearchResults.value = result.data.features;
        console.log(mapboxSearchResults.value);
      } catch {
        searchError.value = true;
      }
      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};
</script> 