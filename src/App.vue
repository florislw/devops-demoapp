<template>
  <PageWrap/>
</template>
<script setup>
import PageWrap from "./components/PageWrap.vue";
import { computed, provide, ref } from "vue";

const pages = [
  {
    slug: 'bursts-emulator',
    title: 'Bursts Emulator',
    menuTitle: 'Bursts'
  },
  {
    slug: 'request-products',
    title: 'What do you need?',
    menuTitle: 'Single'
  }
]


const currentPageSlug = ref( 'bursts-emulator' )

const currentPage = computed( () => {
  return pages.find( p => p.slug === currentPageSlug.value )
} )

provide( 'currentPage', currentPage )
provide( 'currentPageSlug', currentPageSlug )
provide( 'pages', pages )

</script>