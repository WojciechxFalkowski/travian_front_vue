<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-12 col-md-3">
        <div class="form-floating">
          <select v-model="selectedServer" class="form-select" id="floatingSelect"
            aria-label="Floating label select example">
            <!-- <option selected>Server</option> -->
            <option v-for="server of settings.servers" :value="server.name" :key="server.name">{{ server.name }}</option>
          </select>
          <label for="floatingSelect">Server</label>
        </div>
      </div>

      <div class="col-12 col-md-3">
        <div class="form-floating">
          <select v-model="selectedBuilding" class="form-select" id="floatingSelect"
            aria-label="Floating label select example">
            <option v-for="building of settings.buildings" :value="building.name" :key="building.name">{{ building.name }}
            </option>
          </select>
          <label for="floatingSelect">Budynek</label>
        </div>
      </div>

      <div class="col-12 col-md-3">
        <div class="form-floating">
          <select v-model="selectedLevel" class="form-select" id="floatingSelect"
            aria-label="Floating label select example">
            <option v-for="level of settings.levels" :value="level.level" :key="level.level">{{ level.level }}</option>
          </select>
          <label for="floatingSelect">Poziom</label>
        </div>
      </div>

      <div class="col-12 col-md-3">
        <button @click="sendBuildingToQueue" type="button" class="btn btn-primary w-100 h-100">
          Zatwiedź
        </button>
      </div>
    </div>

    <div class="row mt-5">
      <div class="offset-4 col-4">
        <ul class="list-group" v-if="serverBuildingQueue && serverBuildingQueue.length > 0">
          <!-- <li class="list-group-item disabled" aria-disabled="true">A disabled item</li> -->
          <li class="list-group-item" v-for="building of serverBuildingQueue" :key="building.id">
            <p class="mb-0">
              <span>{{ building.buildingName }}</span>
              <span class="ps-1">{{ building.level }}</span>
            </p>
            <button @click="() => deleteBuildingFromQueue(building.id)" type="button" class="btn-close"
              aria-label="Close"></button>
          </li>
        </ul>

        <div v-else>Brak budynków w kolejce</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, type Ref } from 'vue'
import { buildings } from './../utils/buildings'
import { servers } from './../utils/servers'
import { levels } from './../utils/levels'
import { useFetch } from '@vueuse/core'
const baseUrl = import.meta.env.VITE_BASE_URL;

interface BuildingItem {
  server: string
  buildingName: string
  level: number,
  createdAt: Date
  id: number
  updatedAt: Date
}

const buildingQueue = reactive<{ data: BuildingItem[] | null }>({ data: [] })
const fetchBuildingQueue = async () => {
  const { data }: { isFetching: Ref<boolean>, data: Ref<BuildingItem[] | null> } = await useFetch(`${baseUrl}/building-queue`).get().json()//useFetch(`${baseUrl}/building-queue`)
  buildingQueue.data = data.value
}

fetchBuildingQueue()

const settings = reactive({
  servers,
  buildings,
  levels
})

const selectedServer = ref(settings.servers[0].name)
const selectedBuilding = ref(settings.buildings[0].name)
const selectedLevel = ref(settings.levels[0].level)

const serverBuildingQueue = computed(() => {
  if (!buildingQueue.data || buildingQueue.data.length === 0) {
    return []
  }
  return buildingQueue.data.filter((building: BuildingItem) => building.server === selectedServer.value)
})

const deleteBuildingFromQueue = async (buildingId: number) => {
  if (!buildingQueue.data) {
    return
  }
  const selectedBuilding = buildingQueue.data.find(building => building.id === buildingId)

  if (!selectedBuilding || !selectedBuilding.server || !selectedBuilding.buildingName || !selectedBuilding.level) {
    return
  }
  await useFetch(`${baseUrl}/building-queue`).delete({
    server: selectedBuilding.server,
    buildingName: selectedBuilding.buildingName,
    level: selectedBuilding.level
  }).json()

  await fetchBuildingQueue()
}

// const response = fetch('https://pokeapi.co/api/v2/pokemon/ditto').then(res=>{
// 	console.log('res')
// 	console.log(res)
// return res.json()
// }).then(res=>{
// 	console.log('res')
// 	console.log(res)
// })
// console.log('response')
// console.log(response)
const buildingsTravian = [{
  id: '487766',
  name: 'Rezydencja',
  level: '7',
  status: 'normal',
  href: '/build.php?id=19&gid=25',
  slot: '19'
},
{
  id: '487767',
  name: 'Koszary',
  level: '4',
  status: 'normal',
  href: '/build.php?id=20&gid=19',
  slot: '20'
},
{
  id: '487768',
  name: 'Stajnia',
  level: '4',
  status: 'normal',
  href: '/build.php?id=21&gid=20',
  slot: '21'
},
{
  id: '487769',
  name: '',
  level: null,
  status: 'normal',
  href: '/build.php?id=22',
  slot: '22'
},
{
  id: '487770',
  name: 'Warsztat płatnerza',
  level: '3',
  status: 'normal',
  href: '/build.php?id=23&gid=13',
  slot: '23'
},
{
  id: '487771',
  name: 'Akademia',
  level: '5',
  status: 'normal',
  href: '/build.php?id=24&gid=22',
  slot: '24'
},
{
  id: '487772',
  name: 'Kryjówka',
  level: '10',
  status: 'normal',
  href: '/build.php?id=25&gid=23',
  slot: '25'
},
{
  id: '487773',
  name: 'Główny budynek',
  level: '10',
  status: 'normal',
  href: '/build.php?id=26&gid=15',
  slot: '26'
},
{
  id: '487774',
  name: '',
  level: null,
  status: 'normal',
  href: '/build.php?id=27',
  slot: '27'
},
{
  id: '487775',
  name: '',
  level: null,
  status: 'normal',
  href: '/build.php?id=28',
  slot: '28'
},
{
  id: '487776',
  name: '',
  level: null,
  status: 'normal',
  href: '/build.php?id=29',
  slot: '29'
},
{
  id: '487777',
  name: 'Magazyn',
  level: '12',
  status: 'normal',
  href: '/build.php?id=30&gid=10',
  slot: '30'
},
{
  id: '487778',
  name: 'Kryjówka',
  level: '3',
  status: 'normal',
  href: '/build.php?id=31&gid=23',
  slot: '31'
},
{
  id: '487779',
  name: 'Spichlerz',
  level: '8',
  status: 'normal',
  href: '/build.php?id=32&gid=11',
  slot: '32'
},
{
  id: '487780',
  name: 'Kryjówka',
  level: '10',
  status: 'normal',
  href: '/build.php?id=33&gid=23',
  slot: '33'
},
{
  id: '487781',
  name: '',
  level: null,
  status: 'normal',
  href: '/build.php?id=34',
  slot: '34'
},
{
  id: '487782',
  name: 'Kryjówka',
  level: '10',
  status: 'normal',
  href: '/build.php?id=35&gid=23',
  slot: '35'
},
{
  id: '487783',
  name: 'Kryjówka',
  level: '10',
  status: 'normal',
  href: '/build.php?id=36&gid=23',
  slot: '36'
},
{
  id: '487784',
  name: 'Kryjówka',
  level: '10',
  status: 'normal',
  href: '/build.php?id=37&gid=23',
  slot: '37'
},
{
  id: '487785',
  name: 'Młyn',
  level: '1',
  status: 'normal',
  href: '/build.php?id=38&gid=8',
  slot: '38'
},
{
  id: '487786',
  name: 'Miejsce zbiórki',
  level: '1',
  status: 'normal',
  href: '/build.php?id=39&gid=16',
  slot: '39'
},
{
  id: '',
  name: 'Mur obronny',
  level: '2',
  status: 'under construction',
  href: '',
  slot: '40'
},
{
  id: '487787',
  name: 'Mur obronny',
  level: '2',
  status: 'under construction',
  href: '',
  slot: '40'
}]
const sendBuildingToQueue = async () => {
  console.log(selectedServer.value, selectedBuilding.value, selectedLevel.value)
  await useFetch(`${baseUrl}/building-queue`).post({
    server: selectedServer.value,
    buildingName: selectedBuilding.value,
    level: selectedLevel.value
  }).json()

  await fetchBuildingQueue()
}
</script>

<style lang="scss">
.list-group-item {
  display: flex;
  align-items: center;
  justify-content: space-between;

  .btn-close {
    width: 16px;
    height: 16px;
    padding: unset;
  }
}
</style>