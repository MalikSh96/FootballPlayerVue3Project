<template>
  <html class="dark bg-neutral-300">
    <h1 class="text-2xl font-bold text-indigo-600">The Players</h1>
    <div class="relative overflow-x-auto shadow-md sm:rounded-lg">
      <label class="pr-2 italic">Row limit</label>
      <select v-model="perPage" class="border-gray-300 focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 shadow-sm
                                    border-black-100 placeholder-blueGray-300 text-black bg-white rounded text-sm shadow
                                    focus:outline-none focus:ring-indigo-600 ease-linear transition-all duration-150">
        <option v-for="index in setLimitRange" :key="index"> {{ index }}</option>
      </select>
      <h1>page is? {{page}} || display per page is? {{perPage}}</h1>

      <form id="search">
        <input name="query" v-model="searchQuery" class="border-2 mb-5 rounded h-10 p-2" placeholder="Search records">
      </form>
      <table-backup-component :key="page" :columns="columns" :playersData="prevPlayers.ballers.items" 
        :filterKey="searchQuery"/>
      
      <nav aria-label="Page navigation">
        <ul class="inline-flex space-x-2">
            <li class="page-item">
                <button type="button" 
                class="flex items-center justify-center w-10 h-10 text-indigo-600 
                transition-colors duration-150 rounded-full focus:shadow-outline hover:bg-indigo-100" 
                v-if="page != 1" @click="page--; loadJugadores(page, perPage)"
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
                @click="page = pageNumber; loadJugadores(page, perPage);"> {{ pageNumber }} 
                </button>
            </li>
            <li class="page-item">
                <button type="button" @click="page++; loadJugadores(page, perPage);" v-if="page < pagesTotal" class="flex items-center justify-center w-10 h-10 text-indigo-600 
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
import TableBackupComponent from '@/components/helpers/TableBackupComponent.vue'

import {  reactive } from 'vue'

export default {
  components: {
    TableBackupComponent
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
      searchQuery: '',
      columns: [            
        'id',
        'name',
        'age',
        'club',
      ],
			page: 1,
      pagesTotal: null,
      perPage: 20,
      prevPlayers: this.jugadores,
      limit: 20,
    }
  }, 
  methods: {
    async loadJugadores(cP, cL) {
      //fetch is async and returns a promise
      //then() fires a callback function after the fetch is done
      //response.json is also async and returns a promise
      await fetch(`https://futdb.app/api/players?page=${cP}&limit=${cL}`, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.jugadores.ballers = data, this.pagesTotal = data.page_total))
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
    },
    setLimitRange() {
      let range = []
      for(let i = 5; i <= 20; i++)
      {
        range.push(i)
      }
      return range
    }
  },
	watch: { //watch triggers a function whenever a reactive property changes
    perPage(newValue) {
      localStorage.perPage = newValue;
    },
	},
	async created(){
    //To get the data at a "popular" point is to get it inside either the created or mounted lifecycle hook
    //A vue component have lifecycle hooks which fires at different points of their lifecycle
    //The created lifecycle hook is called synchronously after the data has been created
    if(localStorage.perPage) {
      this.perPage = localStorage.perPage
    }
    this.loadJugadores(1, this.perPage)
    this.prevPlayers = this.jugadores
	},
}
</script>

<style scoped>

</style>