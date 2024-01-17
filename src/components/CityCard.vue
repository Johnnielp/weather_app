<template>
    <div
        class="flex py-6 px-3 bg-red-400 rounded-md shadow-md cursor-pointer items-center bg-opacity-0 hover:bg-opacity-30">
        <div class="flex flex-col flex-1 text-yellow-400">
            <h2 class="text-3xl">{{ city.city }}</h2>
            <h3>{{ city.state }}</h3>
        </div>
        <div class="flex flex-col gap-2 text-yellow-400">
            <p class="text-3xl self-end">
                {{ Math.round(city.weather.main.temp) }}&deg;
            </p>
            <span class="flex gap-2">
                H: {{ Math.round(city.weather.main.temp_max) }}&deg;
            </span>
            <span class="flex gap-2">
                L: {{ Math.round(city.weather.main.temp_min) }}&deg;
            </span>
        </div>
    </div>
</template>

<script setup>
defineProps({
    city: {
        type: Object,
        default: () => { },
    }
})
</script>
