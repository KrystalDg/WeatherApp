<template>
  <div>
    <suspense>
      <template #default>
        <async-city-view />
      </template>
      <template #fallback>
        <city-view-skeleton />
      </template>
    </suspense>
  </div>
</template>

<script setup>
import AsyncCityView from "../components/AsyncCityView.vue";
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
</script>
