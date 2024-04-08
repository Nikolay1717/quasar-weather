<template>
  <q-btn
    v-for="location in locations"
    :key="location.id"
    class="location q-ma-xs"
    @click="emit('fetchWeather', location)"
    color="blue"
    push
  >
    <div class="row items-center no-wrap">
      <q-icon left name="place" />
      <div class="text-center">
        {{ location.name }}<br />{{ location.country }}
      </div>
    </div>
  </q-btn>
</template>

<script setup>
defineProps({
  locations: Array,
});

const emit = defineEmits(['fetchWeather']);
</script>

<style scoped>
.location:hover {
  scale: 1.05;
}
.location {
  transition: ease 0.2s;
}
</style>
