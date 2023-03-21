<template>
  <header class="min-h-full">
    <Disclosure as="nav" class="bg-white border-b border-gray-200" v-slot="{ open }">
      <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <div class="grid grid-cols-12 h-16 items-center">
          <div class="flex items-center col-span-2">
            <div class="flex-shrink-0 flex items-center gap-4 cursor-pointer" id="app-title" @click="currentPageSlug = 'bursts-emulator'">

              <svg class="w-8 h-8 fill-sky-900" xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="103.6 100.8 492.81 358.4">
                <path d="m275.16 100.81c-2.9102 0.10547-5.6719 1.332-7.6875 3.4336l-61.719 62.285c-1.5078-4.5977-5.793-7.707-10.633-7.7109h-45.895c-25.688 0-45.621 22.641-45.621 49.094v63.16c0 26.453 19.934 49.094 45.621 49.094h45.895c6.1836 0 11.199-5.0156 11.199-11.199v-0.38281h92.051c3.5664 0.0625 6.9414-1.6055 9.0898-4.4492l67.387-68.016c8.7734-8.8516 13.68-20.148 14.746-31.738h52.203c28.312 0 51.352-23.324 51.352-51.73s-23.039-51.73-51.352-51.73h-162.21c-0.59375-0.003907-1.1836 0.0625-1.7734 0.12891-0.87109-0.19141-1.7578-0.26172-2.6484-0.24219zm4.9453 22.508h161.69c16.18 0 28.953 12.836 28.953 29.332 0 16.496-12.773 29.332-28.953 29.332h-55.082c-2.4727-6.9141-6.3867-13.414-11.867-18.941-9.9805-10.074-23.16-15.102-36.344-15.102s-26.375 5.0312-36.355 15.102l-23.582 23.797c-10.516 10.609 5.3984 26.379 15.914 15.77l23.582-23.809c11.5-11.605 29.375-11.605 40.875 0s11.5 29.953 0 41.559l-65.219 65.828h-87.391v-88.566c0.46484-0.35156 0.90625-0.74609 1.3125-1.1719zm224.79 116.51c-6.1836 0-11.199 5.0156-11.199 11.199v0.37109h-92.062c-3.582-0.0625-6.9688 1.6289-9.1094 4.4961l-67.352 67.984c-8.7734 8.8516-13.68 20.148-14.746 31.738h-52.203c-28.312 0-51.352 23.324-51.352 51.73s23.039 51.73 51.352 51.73h162.21c0.54688 0 1.0977-0.0625 1.6406-0.12891 3.8086 0.82422 7.7734-0.38672 10.469-3.2031l61.719-62.297c1.5039 4.6016 5.793 7.7148 10.633 7.7227h45.895c25.688 0 45.621-22.641 45.621-49.098v-63.16c0-26.453-19.934-49.094-45.621-49.094zm-98.578 33.969h87.379v88.578c-0.46875 0.35156-0.91406 0.74609-1.3242 1.1719l-72.461 73.133h-161.69c-16.18 0-28.953-12.836-28.953-29.332s12.773-29.332 28.953-29.332h55.082c2.4727 6.9141 6.3867 13.414 11.867 18.941 19.961 20.145 52.742 20.145 72.703 0l23.582-23.797c10.52-10.609-5.3945-26.379-15.914-15.77l-23.582 23.797c-11.5 11.605-29.375 11.605-40.875 0s-11.5-29.941 0-41.551z"/>
              </svg>
              <div class="text-sky-900 font-bold text-xl">
                AidSupply
              </div>
            </div>
          </div>

          <div class="col-span-8 flex">
            <div class="mx-auto flex items-baseline space-x-4">
              <button v-for="page in pages" :key="page.slug"
                      @click="currentPageSlug = page.slug"
                      :class="[page.slug === currentPageSlug ? 'bg-gray-200' : 'hover:bg-gray-200', 'rounded-md px-3 py-2 text-sm font-medium']"
                      :aria-current="page.slug === currentPageSlug ? 'page' : undefined">{{
                  page.menuTitle || page.title
                }}
              </button>
            </div>
          </div>

          <div class="col-span-2">
            <button @click="currentPageSlug = 'config'"
                    :class="['config' === currentPageSlug ? 'bg-gray-200' : 'hover:bg-gray-200', 'block ml-auto rounded-md px-3 py-2 text-sm font-medium']"
                    :aria-current="'config' === currentPageSlug ? 'page' : undefined">
              <Cog6ToothIcon class="h-6 w-6" />
            </button>
          </div>
        </div>
      </div>
    </Disclosure>


  </header>
</template>

<script setup>
import { Disclosure } from '@headlessui/vue'
import { Cog6ToothIcon } from '@heroicons/vue/24/outline'
import { inject } from "vue";

const pages = inject( 'pages' )
const currentPageSlug = inject( 'currentPageSlug' )
</script>

<style scoped>
#app-title {
  text-decoration: underline;
  text-decoration-color: #bae6fd;
  text-decoration-thickness: .6em;
  text-decoration-skip-ink: none;
  text-underline-offset: -.4em;
}
</style>