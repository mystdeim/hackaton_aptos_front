<template>
  <q-page class="q-pa-lg">
<!--    <q-list dense>-->
<!--      <q-item v-for="(item, index) in items" :key="`row-${index}`" clickable v-ripple @click="show(item.id)" >-->
<!--        <q-item-section>{{ item.id.slice(0, 50) }}</q-item-section>-->
<!--      </q-item>-->
<!--    </q-list>-->

    <template v-for="(item, index) in items" :key="`row-${index}`" clickable v-ripple @click="show(item.id)" >-
      <q-card v-if="hasImg(item)" class="my-card" dense>
        <q-card-section horizontal>
          <q-img
              class="col-5"
            :src="item['meta']['content'][0]['url']"
          />
<!--          {{ JSON.stringify(item['meta']['content'][0]['url']) }}-->
          <q-card-section>
<!--            {{ item.id.slice(0, 50) }}-->
            <div class="text-h6">{{ item['meta']['name'] }}</div>
            <div class="text-subtitle4 word-wrap">{{ item.id }}</div>
            <q-chip color="teal" text-color="white" icon="bookmark">
              Supply = {{ item['supply'] }}
            </q-chip>

<!--            <q-chip v-for="(prop, index) in item['meta']" :key="`row-${index}`"  color="teal" text-color="white" icon="bookmark">-->
<!--              Bookmark = a-->
<!--            </q-chip>-->

          </q-card-section>
        </q-card-section>
      </q-card>
    </template>

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

<style scoped>
.word-wrap {
  overflow-wrap: break-word;
  word-break: break-all;
}
</style>

<script setup>

import { onMounted, ref } from 'vue'
import axios from "axios";

const items = ref([{id: '1'}])
const count = ref(10)
const alert = ref(false)
const img = ref("")
const meta = ref({})
const metas = ref([])
const api = axios.create({
  baseURL: 'http://localhost:8081'
})

const pull = async() => {
  const reponse = await api.get(`/v0.1/items/all?size=10`)
  items.value = reponse.data['items']
  metas.value = []
  for (let i = 0; i < reponse.data['items'].length; i++) {
    let item = reponse.data['items'][i]
    metas.value[i] = (await getMeta(item.id)).data
  }
}

const hasImg = (item) => {
    return 'meta' in item && 'content' in item['meta']
    // return true
}

const show = async (id) => {
  alert.value = true
  await getMeta(id)
}

const getMeta = async(id) => {
  console.log(`Get ${id}`)
  const reponse = await api.get(`/v0.1/items/${id}`)
  img.value = reponse.data['meta']['content'][0]['url']
  meta.value = reponse.data['meta']
  return reponse.data
}

onMounted(async () => {
  await pull()
})

</script>
