<template>
  <html class="dark">
    <!-- {{jugadores.ballers.items[0]}} -->
    <h1 class="text-2xl font-bold text-indigo-600">The Players</h1>
    <div class="relative overflow-x-auto shadow-md sm:rounded-lg">
      <label class="pr-2 italic">Row limit</label>
      <select v-model="perPage" class="border-gray-300 focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 shadow-sm
                                    border-black-100 placeholder-blueGray-300 text-black bg-white rounded text-sm shadow
                                    focus:outline-none focus:ring-indigo-600 ease-linear transition-all duration-150">
        <option v-for="(page, key) in jugadores.ballers.items.length" :key="key"> {{ page }}</option>
      </select>
      <!-- <h1>check: {{displayedPlayers}}</h1> -->
      <h1>page is? {{page}} || limit is? {{limit}}</h1>
      <table-component :key="page" :PlayersData="prevPlayers.ballers.items" :UpdatedPlayers="newPlayers.items"/>
      <!-- <pagination-component :PlayersData="prevPlayers"/> -->
      <nav aria-label="Page navigation">
        <ul class="inline-flex space-x-2">
            <li class="page-item">
                <button type="button" 
                class="flex items-center justify-center w-10 h-10 text-indigo-600 
                transition-colors duration-150 rounded-full focus:shadow-outline hover:bg-indigo-100" 
                v-if="page != 1" @click="page--; getPlayers(page)"
                > 
                <svg class="w-4 h-4 fill-current" viewBox="0 0 20 20">
                    <path d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 
                    010-1.414l4-4a1 1 0 011.414 0z" clip-rule="evenodd" fill-rule="evenodd">
                    </path>
                </svg>
                </button>
            </li>
            <li class="page-item">
                <button type="button" class="w-10 h-10 text-indigo-600 transition-colors duration-150 
                rounded-full focus:shadow-outline hover:bg-indigo-100" 
                v-for="(pageNumber, key) in setPages" :key="key" 
                @click="page = pageNumber; getPlayers(page);"> {{ pageNumber }} 
                </button>
            </li>
            <li class="page-item">
                <button type="button" @click="page++; getPlayers(page);" v-if="page < pagesTotal" class="flex items-center justify-center w-10 h-10 text-indigo-600 
                transition-colors duration-150 bg-white rounded-full focus:shadow-outline hover:bg-indigo-100"
                >
                <svg class="w-4 h-4 fill-current" viewBox="0 0 20 20">
                    <path d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 
                    010 1.414l-4 4a1 1 0 01-1.414 0z" clip-rule="evenodd" fill-rule="evenodd">
                    </path>
                </svg>
                </button>
            </li>
          <p class="py-2 italic">Current page: {{ page }}</p>
          <p class="py-2 italic">|| Total pages: {{ pagesTotal }}</p>
        </ul>
      </nav>

    </div>
  </html>
</template>

<script>
import TableComponent from '@/components/helpers/TableComponent.vue'
// import PaginationComponent from '@/components/helpers/PaginationComponent.vue'

import {  reactive } from 'vue'

export default {
  components: {
    TableComponent,
    // PaginationComponent
  },
  setup() {
    //const, because obect itself won't change, just the properties inside
    const jugadores = reactive({
      ballers: []
    })
    return { jugadores }
  },
  data() {
    return {
			page: 1,
      pagesTotal: null,
      // pages: [],
      perPage: 20,
      testPages: [1,2,3,4,5],
      newPlayers: [],
      prevPlayers: this.jugadores,
      limit: 20,
    }
  }, 
  methods: {
    async loadJugadores(cP,cL) {
      // console.log('PARENT BEFORE FETCH page ' + cP + ' and limit ' + cL)
      await fetch(`https://futdb.app/api/players?page=${cP}&limit=${cL}`, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.jugadores.ballers = data))
      .catch(err => console.log(err.message))
      // console.log('PARENT AFTER FETCH page ' + this.page + ' and limit ' + this.perPage)
      // console.log('PARENT fetched jugadores inside our method', this.prevPlayers.ballers.items)
      // this.setPages()
    },
    async getPagesTotal() { //gets page total
      await fetch(`https://futdb.app/api/players`, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.pagesTotal = data.page_total))
      .catch(err => console.log(err.message))
      // console.log('AFTER FETCH PAGES TOTAL', this.pagesTotal)
    },
    async getPlayers(page) {
      // await this.getPagesTotal() // maybe don't call this everytime, move it later
      console.log('PARENT BEFORE FETCH GET PLAYERS ' + page)
      await fetch(`https://futdb.app/api/players?page=${page}`, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.newPlayers = data))
      .catch(err => console.log(err.message))
    },

  },
  computed: {
    setPages() {
      let total = []
      for(let num = 1; num <= this.pagesTotal; num++) {
        total.push(num)
      }
      return total.slice(this.page-1, this.page+5)
    }
  },
	watch: { //watch triggers a function whenever a reactive property changes
    page(newPage, oldPage) {
      console.log('PARENT new page', newPage)
      console.log('PARENT old page', oldPage)
    },
    prevPlayers() {
      console.log('PARENT inside prev players watch', this.prevPlayers.items)
      // console.log('inside prev players watch AFTER', this.prevPlayers.items)
    },
    newPlayers() {
      // console.log('what is new page value?', this.page)
      console.log('PARENT what is the NEW player data', this.newPlayers.items)
    },
	},
	created(){
    this.getPagesTotal()
    this.loadJugadores(1, 20)
    this.prevPlayers = this.jugadores
    console.log('PARENT created', this.prevPlayers)
	},
  async beforeUpdate(){
    console.log('PARENT BEFORE UPDATE CALLED', this.newPlayers)
  }
}
</script>

<style scoped>

</style>