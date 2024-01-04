<template>
    <div>

    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';
import * as constants from '../extra/constants.js';

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${constants.OPENWEATHER_API_KEY}`);//&units=imperial
    } catch (error) {
        console.log(error);
    }
};
</script>
