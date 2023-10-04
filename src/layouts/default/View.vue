<style>
.page-title {
  padding: 1rem 4rem;
}
</style>

<template>
  <v-main>
    <h1 class="page-title">{{ $router.currentRoute.value.name }}</h1>
    <router-view />
  </v-main>
</template>

<script lang="ts" setup>
//
</script>
