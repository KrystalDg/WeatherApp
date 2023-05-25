<template>
  <div>
    <suspense>
      <async-city-view />
      <template #fallback>
        <div class="flex w-full items-center justify-center text-white py-12 ">
          <p>Loading...</p>
        </div>
      </template>
    </suspense>
  </div>
</template>

<script setup>
import AsyncCityView from "../components/AsyncCityView.vue";
</script>
