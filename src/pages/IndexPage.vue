<template>
  <q-page class="flex">
    <q-list dense>
      <q-item v-for="(item, index) in items" :key="`row-${index}`" clickable v-ripple @click="show(item.id)" >
        <q-item-section>{{ item.id.slice(0, 50) }}</q-item-section>
      </q-item>
    </q-list>

    <q-dialog v-model="alert">
      <q-card>
        <q-card-section>
          <div class="text-h6">{{ meta['name'] }}</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
            <q-img :src="img"></q-img>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

  </q-page>
</template>

<script setup>

import { onMounted, ref } from 'vue'
import axios from "axios";

const items = ref([{id: '1'}])
const count = ref(10)
const alert = ref(false)
const img = ref("")
const meta = ref({})
const api = axios.create({
  baseURL: 'http://localhost:8081'
})

const pull = async() => {
  const reponse = await api.get(`/v0.1/items/all?size=20`)
  items.value = reponse.data['items']
}

const show = async (id) => {
  alert.value = true
  await getMeta(id)
}

const getMeta = async(id) => {
  const reponse = await api.get(`/v0.1/items/${id}`)
  img.value = reponse.data['meta']['content'][0]['url']
  meta.value = reponse.data['meta']
}

onMounted(async () => {
  await pull()
})

</script>
