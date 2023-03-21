<template>
  <Header />
  <PageHeading>
    {{ title || currentPage?.title }}
  </PageHeading>
  <main>
    <div class="mx-auto max-w-7xl py-6 sm:px-6 lg:px-8">
      <slot>

      </slot>
    </div>
  </main>
  <Footer />
</template>

<script setup>
import Header from "../layout-parts/Header.vue";
import PageHeading from "../layout-parts/PageHeading.vue";

import { inject } from "vue";
import Footer from "../layout-parts/Footer.vue";

const currentPage = inject( 'currentPage' )
defineProps({
  title: String
})

</script>

<style scoped>

</style>