<template>
  <BurstsEmulator v-if="currentPageSlug === 'bursts-emulator'" />
  <RequestProducts v-else-if="currentPageSlug === 'request-products'" />
  <Config v-else-if="currentPageSlug === 'config'" />
  <div v-else class="flex">
    <h1 class="text-4xl font-bold mx-auto my-12">404 Page Not Found</h1>
  </div>
</template>

<script setup>
import { inject } from "vue";
import BurstsEmulator from "./pages/BurstsEmulator.vue";
import RequestProducts from "./pages/RequestProducts.vue";
import Config from "./pages/Config.vue";

const currentPageSlug = inject('currentPageSlug')
</script>
