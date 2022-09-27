<template>
  <DropDown
    v-model="selectedDropDown"
    :options="countryData"
    class="flex flex-col items-center mt-10 max-w-[300px] w-full mx-auto"
  />
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import DropDown from '@/components/DropDown.vue';

const selectedDropDown = ref('');
const countryData = ref([]);

onMounted(() => {
  fetch('/country.json')
    .then((response) => response.json())
    .then((data) => (countryData.value = data));
});
</script>

<style lang="scss"></style>
