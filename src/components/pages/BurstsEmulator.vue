<template>
  <AppLayout>
    <Card>
      <form @submit.prevent="doLoadTest" v-if="endpoint">
        <Input label="Requests per second" type="number" min="1" max="50" required v-model="rPerSec"/>
        <Input label="Load test duration (s)" class="mt-4" type="number" required v-model="duration"/>
        <Button type="submit" class="mt-4 ml-auto" :disabled="isRunning">
          Start
        </Button>
      </form>
      <p v-else>
        <span class="underline text-blue-700 cursor-pointer" @click="currentPageSlug = 'config'">
          Define an endpoint</span> to begin.
      </p>
    </Card>

    <Card class="mt-8">
      <h2 class="font-bold text-lg mb-4">
        Results
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
            {{ !isRunning ? (actualDuration + ' seconds') : '' }}
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
            {{ durationSum / testLog.length || 0 }} ms
          </td>
        </tr>
        <tr>
          <td class="pr-4">
            Median
          </td>
          <td>
            {{ median( testLog.map( li => li.duration | 0 ) ) }} ms
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
import { computed, inject, ref } from "vue";

const currentPageSlug = inject( 'currentPageSlug' )
const endpoint = localStorage.getItem( 'endpoint' )

const rPerSec = ref( 1 )
const duration = ref( 1 )
let currentInterval = null
const isRunning = computed( () => currentInterval !== null )

const actualDuration = ref(null)
let testLog = ref( [] )
const durationSum = computed( () => {
  return testLog.value
      .map( li => li.duration | 0 )
      .reduce( ( a, b ) => a + b, 0 )
} )

const doSingleRequest = async () => {
  const startTime = Date.now()

  try {
    const response = await fetch( endpoint, {
      mode: 'no-cors',
      method: 'GET',
      headers: {
        'Accept': 'application/json',
      },
    } )

    testLog.value.push( {
      startTime,
      duration: Date.now() - startTime,
      ok: response.ok
    } )
  } catch ( err ) {

    console.error( err )
    testLog.value.push({
      startTime,
      duration: Date.now() - startTime
    })
  }
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

  currentInterval = setInterval( doSingleRequest, intervalMs )
  setTimeout( () => {
    actualDuration.value = (Date.now() - startTime) / 1000
    stopLoadTest()
  }, durationMs )
}

const stopLoadTest = () => {
  return currentInterval !== null && clearInterval( currentInterval )
}

function median(arr) {
  if (arr.length === 0) {
    return; // 0.
  }
  arr.sort((a, b) => a - b)
  const midpoint = Math.floor(arr.length / 2)
  return arr.length % 2 === 1 ?
      arr[midpoint] :
      (arr[midpoint - 1] + arr[midpoint]) / 2
}
</script>

<style scoped>

</style>