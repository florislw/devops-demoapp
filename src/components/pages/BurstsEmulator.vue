<template>
  <AppLayout>
    <Card>
      <form @submit.prevent="doLoadTest" v-if="endpoint">
        <div class="mb-4">
          Endpoint:
          <input class="inline-block" disabled :value="endpoint"/>
          &nbsp;
          <span class="underline text-blue-700 cursor-pointer" @click="currentPageSlug = 'config'">Change</span>
        </div>

        <Input label="Requests per second" type="number" min="1" max="50" required v-model="rPerSec"/>
        <Input label="Load test duration (s)" class="mt-4" type="number" required v-model="duration"/>
        <div class="flex mt-4 justify-end">
          <Button type="button" class="mr-4" @click="stopLoadTest" v-if="isRunning">
            Stop
          </Button>
          <Button type="submit" :disabled="isRunning">
            Start
          </Button>
        </div>
      </form>
      <p v-else>
        <span class="underline text-blue-700 cursor-pointer" @click="currentPageSlug = 'config'">
          Define an endpoint</span> to begin.
      </p>
    </Card>

    <Card class="mt-8" v-if="testLog.length > 0">
      <h2 class="font-bold text-lg mb-4">
        Summary
      </h2>
      <table>
        <tr>
          <td class="font-bold pr-4">
            Number of requests
          </td>
          <td colspan="2">
            {{ testLog.length }}
          </td>
        </tr>
        <tr>
          <td class="font-bold pr-4">
            Duration
          </td>
          <td colspan="2">
            {{ isRunning ?  'running...' : (actualDuration + ' seconds') }}
          </td>
        </tr>
        <tr>
          <td rowspan="3" class="align-top font-bold pr-4">
            Request duration
          </td>
          <td class="pr-4">
            Sum
          </td>
          <td>
            {{ durationSum }} ms
          </td>
        </tr>
        <tr>
          <td class="pr-4">
            Mean
          </td>
          <td>
            {{ durationMean }} ms
          </td>
        </tr>
        <tr>
          <td class="pr-4">
            Median
          </td>
          <td>
            {{ durationMedian }} ms
          </td>
        </tr>
      </table>
    </Card>
    <Card class="mt-8" v-if="testLog.length > 0">

      <h2 class="font-bold text-lg mb-4">
        Requests
      </h2>

      <div class="mt-4">
        <Input label="Show all requests" id="show-all-requests" v-model="tableShowAll" type="checkbox"/>
      </div>

      <table class="mt-8 w-full">
        <tr class="font-bold text-left border-b border-gray-300">
          <th>

          </th>
          <th>
            Duration (ms)
          </th>
          <th>
            X-Generated-By
          </th>
          <th>
            Status
          </th>

        </tr>
        <tr v-for="(li, idx) in testLogTableData" :key="idx" class="border-b border-gray-300">
          <td>
            {{ testLog.length - idx }}
          </td>
          <td>
            <div class="flex items-center mr-8">
              <meter min="0" :max="durationMax" optimum="0" :low="Math.round(durationMean)"
                     :high="Math.round(durationMean) * 1.5" :value="li.duration" class="mr-2 w-full"/>
              <span class="min-w-[6ch]">{{ li.duration }}</span>
            </div>
          </td>
          <td>
            {{ li.serverHeader }}
          </td>
          <td>
            <CheckIcon v-if="li.ok" class="w-4 h-4 text-green-800 inline"/>
            <XMarkIcon v-else class="w-4 h-4 text-red-800 inline"/>
            {{ li.status }} {{ li.statusText }}
          </td>
        </tr>
      </table>
    </Card>
  </AppLayout>
</template>

<script setup>
import AppLayout from "../layouts/AppLayout.vue";
import Card from "../containers/Card.vue";
import Button from "../forms/Button.vue";
import Input from "../forms/Input.vue";
import { CheckIcon, XMarkIcon } from "@heroicons/vue/24/outline/index.js";
import { computed, inject, onBeforeUnmount, ref } from "vue";

const currentPageSlug = inject( 'currentPageSlug' )
const endpoint = localStorage.getItem( 'endpoint' )

const tableShowAll = ref( false )

const rPerSec = ref( 1 )
const duration = ref( 1 )
let currentInterval = ref(null)
const isRunning = computed( () => currentInterval.value !== null )

const actualDuration = ref( null )
let testLog = ref( [] )

const testLogTableData = computed( () => {
  const {value} = testLog
  if ( ! value ) {
    return []
  }

  const data = tableShowAll.value ? value : value.slice( Math.max( value.length - 5, 0 ) )

  return data.reverse()
} )

const durations = computed( () => testLog.value.map( li => li.duration | 0 ) )
const durationSum = computed( () => durations.value.reduce( ( a, b ) => a + b, 0 ) )
const durationMean = computed( () => durationSum.value / testLog.value.length || 0 )
const durationMedian = computed( () => median( durations.value ) )
const durationMax = computed( () => Math.max( ...durations.value ) )

const doSingleRequest = async () => {
  const startTime = Date.now()

  fetch( endpoint, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json',
    },
    body: JSON.stringify( {
      area: 'string',
      name: 'string',
      supplies: [
        {
          amount: 100,
          productid: 0
        }
      ]
    } )
  } )
      .then( response => {
        testLog.value.push( {
          startTime,
          duration: Date.now() - startTime,
          ok: response.ok,
          status: response.status,
          statusText: response.statusText,
          serverHeader: response.headers.get( 'X-Generated-By' )
        } )
      } )
      .catch( error => {
        if ( error instanceof Response ) {
          testLog.value.push( {
            startTime,
            duration: Date.now() - startTime,
            ok: error.ok,
            status: error.status,
            statusText: error.statusText,
            serverHeader: error.headers.get( 'X-Generated-By' )
          } )
        } else {
          testLog.value.push( {
            startTime,
            duration: Date.now() - startTime,
            ok: false,
          } )
        }
      } )
}
const doLoadTest = () => {
  const intervalMs = 1000 / parseFloat( rPerSec.value )
  const durationMs = 1000 * parseFloat( duration.value )

  if ( isNaN( intervalMs ) || isNaN( durationMs ) ) {
    alert( 'Could not parse interval and duration values' )
    return
  }

  testLog.value = []
  const startTime = Date.now()

  currentInterval.value = setInterval( doSingleRequest, intervalMs )
  setTimeout( () => {
    actualDuration.value = (Date.now() - startTime) / 1000
    stopLoadTest()
  }, durationMs )
}

const stopLoadTest = () => {
  currentInterval.value !== null && clearInterval( currentInterval.value )
  currentInterval.value = null
}

function median( arr ) {
  if ( arr.length === 0 ) {
    return; // 0.
  }
  arr.sort( ( a, b ) => a - b )
  const midpoint = Math.floor( arr.length / 2 )
  return arr.length % 2 === 1 ?
      arr[midpoint] :
      (arr[midpoint - 1] + arr[midpoint]) / 2
}

onBeforeUnmount( stopLoadTest )
</script>

<style scoped>

</style>