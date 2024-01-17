<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-yellow-400 bg-opacity-50 p-4 bg-red-400 w-full text-center">
            <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
        </div>
        <!-- Weather Overview -->
        <div class="flex flex-col items-center text-yellow-400 text-2xl py-12">
            <h1 class="text-5xl mb-2">{{ route.params.city }}</h1>
            <p class=" mb-12">
                {{
                    new Date(weatherData.currentTime).toLocaleString("en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleString("en-us",
                        {
                            timeStyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-24">
                {{ Math.round(weatherData.current.temp) }}&deg;
            </p>

            <p class="text-4xl">
                Feels like {{ Math.round(weatherData.current.feels_like) }}&deg;
            </p>
            <p class="capitalize">
                {{ weatherData.current.weather[0].description }}
            </p>
            <img class="w-[150px] h-auto"
                :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" alt="">

        </div>
        <hr class="border-white border-opacity-10 border w-full" />
        <!-- Hourly weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-yellow-400">
                <h2 class="mb-4 text-4xl pb-4">Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div v-for="hourData in weatherData.hourly" :key="hourData.dt"
                        class="flex flex-col gap-4 items-center text-xl">
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(
                                    hourData.currentTime
                                ).toLocaleString("en-us", {
                                    hour: "numeric",
                                })
                            }}
                        </p>
                        <img class="w-auto h-[50px] object-cover" :src="`
                        http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`" alt="">
                        <p class="text-xl pb-2">
                            {{ Math.round(hourData.temp) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <hr class="border-white border-opacity-10 border w-full">
        <!-- Weekly Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-yellow-400 text-2xl">
                <h2 class="mb-4 text-4xl pb-4">7 Day Forecast</h2>
                <div v-for="day in weatherData.daily" :key="day.dt" class="flex items-center">
                    <p class="flex-1">
                        {{ new Date(day.dt * 1000).toLocaleString("en-us",
                            {
                                weekday: "long",
                            }) }}
                    </p>
                    <img class="w-auto h-[50px] object-cover" :src="`
                        http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt="">
                    <div class="flex gap-2 flex-1 justify-end">
                        <p>H: {{ Math.round(day.temp.max) }}</p>
                        <p> - </p>
                        <p>L: {{ Math.round(day.temp.min) }}</p>
                    </div>
                </div>
            </div>
        </div>
        <div class="flex items-center gap-2 py-12 text-yellow-400 cursor-pointer duration-150 hover:text-red-500"
            @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`);

        //calc current date & time

        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;

        //calc hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        });

        // Flicker Delay
        await new Promise((res) => setTimeout(res, 1000));


        return weatherData.data;
    } catch (err) {
        console.log(err);
    }
}
const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCities"));
    const updatedCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem("savedCities", JSON.stringify(updatedCities));
    router.push({
        name: "home",
    });
};
</script>

