<template>
    <div class="flex py-6 px-3 bg-red-400 bg-opacity-30 rounded-md shadow-md items-center">
        <div class="flex flex-col flex-1 gap-2">
            <AnimatedPlaceholder class="max-w-[50%] rounded-lg" />
            <AnimatedPlaceholder class="max-w-[40%] rounded-lg" />
        </div>
        <div class="flex flex-col items-end flex-1 gap-2">
            <AnimatedPlaceholder class="max-w-[50px] w-full rounded-lg" />
            <AnimatedPlaceholder class="max-w-[75px] w-full rounded-lg" />
        </div>
    </div>
</template>

<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';


</script>
