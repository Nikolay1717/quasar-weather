<template>
  <q-page class="flex q-pl-md column">
    <SeachCity @fetchItems="fetchItems" />
    <div>
      <Location :locations="locations" @fetchWeather="fetchWeather" />
    </div>
    <BarChart
      :chartData="chartData"
      :chartOptions="chartOptions"
      style="max-height: 400px"
    />
  </q-page>
</template>

<script setup>
import { api } from 'src/boot/axios';
import { ref } from 'vue';
import moment from 'moment-timezone';

import Location from 'src/components/Location.vue';
import SeachCity from 'src/components/SearchCity.vue';
import BarChart from 'src/components/BarChart.vue';

defineOptions({
  name: 'IndexPage',
});

const locations = ref([]);
const chartData = ref({
  datasets: [],
});
const chartOptions = {
  responsive: true,
};

const fetchItems = async (city) => {
  if (!city) return;
  try {
    const res = await api.get(
      `https://geocoding-api.open-meteo.com/v1/search?name=${city}&count=10&language=en&format=json`
    );
    if (res.error) {
      locations.value = [];
      console.log(res.reason);
      return;
    }
    locations.value = res.data.results;
  } catch (error) {
    console.log(error.message);
  }
};

const fetchWeather = async (selectedLocation) => {
  if (!selectedLocation) return;
  const timezone = moment.tz.guess();
  try {
    const res = await api.get(
      `https://api.open-meteo.com/v1/forecast?latitude=${selectedLocation.latitude}&longitude=${selectedLocation.longitude}&current=temperature_2m&hourly=temperature_2m&timezone=${timezone}`
    );
    if (res.error) {
      chartData.value = {
        datasets: [],
      };
      console.log(res.reason);
      return;
    }

    const currentTemperature = res.data.current.temperature_2m;
    const timeStamps = res.data.hourly.time.filter(
      (item, index) => index % 6 === 0
    );
    const weatherLabels = timeStamps.map((item) => item.replace('T', ' '));
    const weatherData = res.data.hourly.temperature_2m.filter(
      (item, index) => index % 6 === 0
    );

    chartData.value = {
      labels: weatherLabels,
      datasets: [
        {
          label: `${selectedLocation.name} | ${selectedLocation.timezone} (Current: ${currentTemperature} °C)`,
          backgroundColor: 'rgb(255, 99, 132)',
          data: weatherData,
        },
      ],
    };
  } catch (error) {
    console.log(error.message);
  }
};
</script>
