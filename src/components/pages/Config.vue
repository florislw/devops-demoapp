<template>
  <AppLayout title="Configuration">
    <Card>
      <form @submit.prevent="saveEndpoint">
        <Input label="Request endpoint" type="url" v-model="endpoint"/>

        <div class="flex items-center mt-4">
          <Button type="submit">
            Save
          </Button>
          <div class="ml-4 text-green-600" v-if="isSaved">
            Saved in local storage
          </div>
        </div>
      </form>
    </Card>
  </AppLayout>
</template>

<script setup>
import AppLayout from "../layouts/AppLayout.vue";
import Card from "../containers/Card.vue";
import Input from "../forms/Input.vue";
import { ref, watch } from "vue";
import Button from "../forms/Button.vue";

const endpoint = ref(localStorage.getItem('endpoint') || '')

const isSaved = ref(false)
const saveEndpoint = async () => {
  await localStorage.setItem( 'endpoint', endpoint.value )
  isSaved.value = true
}

watch(endpoint, () => {
  isSaved.value = false
})
</script>

<style scoped>

</style>