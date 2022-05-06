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
      <!-- <table-component :key="page" :PlayersData="displayedPlayers"/> -->
      <pagination-component :PlayersData="jugadores.ballers.items"/>
      <!-- <nav aria-label="Page navigation">
        <ul class="inline-flex space-x-2">
          <li class="page-item">
            <button type="button" 
              class="flex items-center justify-center w-10 h-10 text-indigo-600 
              transition-colors duration-150 rounded-full focus:shadow-outline hover:bg-indigo-100" 
              v-if="page != 1" @click="page--; loadJugadores(page, limit);"
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
              v-for="(pageNumber, key) in pages.slice(page-1, page+5)" :key="key" 
              @click="page = pageNumber; loadJugadores(pageNumber, limit);"> {{ pageNumber }} 
            </button>
          </li>
          <li class="page-item">
            <button type="button" @click="page++; loadJugadores(page, limit);" v-if="page < pages.length" class="flex items-center justify-center w-10 h-10 text-indigo-600 
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
          <p class="py-2 italic">|| Total pages: {{ jugadores.ballers.page_total }}</p>
        </ul>
      </nav>	 -->
    </div>
  </html>
</template>

<script>
// import TableComponent from '@/components/helpers/TableComponent.vue'
import PaginationComponent from '@/components/helpers/PaginationComponent.vue'

import { onBeforeUpdate, reactive } from 'vue'

export default {
  components: {
    // TableComponent,
    PaginationComponent
  },
  setup() {
    //const, because obect itself won't change, just the properties inside
    const jugadores = reactive({
      ballers: []
    })
    onBeforeUpdate(() => {
      console.log('beforeUpdate', jugadores.ballers)
      jugadores
    })
    // onUpdated(() => { //composition api way
    //   console.log('updating dom ', jugadores.ballers)
    //   jugadores.ballers
    //   // return this.paginate(Object.values(this.players.items));
    // })
    return { jugadores }
  },
  data() {
    return {
			page: 1,
			perPage: 20,
			pages: [],		
      players: [],
      limit: 20,
      componentKey: 0
    }
  }, 
  methods: {
    async loadJugadores(cP,cL) {
      console.log('BEFORE FETCH page ' + cP + ' and limit ' + cL)
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
      console.log('AFTER FETCH page ' + this.page + ' and limit ' + this.perPage)
      console.log('fetched jugadores inside our method', this.jugadores.ballers)
      // this.setPages()
    },
    setPages () {
      if(localStorage.perPage) {
        this.perPage = localStorage.perPage
      }
      // console.log('pushing p')
			for (let index = 1; index <= this.jugadores.ballers.page_total; index++) {
				this.pages.push(index);
			}
		},
		paginate (jugadores) {
      // console.log('paginate')
			let page = this.page;
			let perPage = this.perPage;
			let from = (page * perPage) - perPage;
			let to = (page * perPage);
			return jugadores.slice(from, to);
		},
  },
  computed: {
		displayedPlayers () {
      //Converting object to array
      console.log('page total', this.jugadores.ballers.page_total)
      console.log('new updated jugadores', this.jugadores.ballers.items)
			return this.paginate(Object.values(this.jugadores.ballers.items));
		},
	},
	watch: { //watch triggers a function whenever a reactive property changes
    perPage(newValue) {
      localStorage.perPage = newValue;
    },
    page (newPage) {
      console.log('new page', newPage)
      this.page = newPage
    },
    limit (newLimit) {
      //not reacting to limit change right now for some reason
      console.log('new limit', newLimit)
      this.limit = newLimit
    }
	},
	created(){
    console.log('created in parent')
    //To get the data at a "popular" point is to get it inside either the created or mounted lifecycle hook
    //A vue component have lifecycle hooks which fires at different points of their lifecycle
    //The mounted lifecycle hook fires when the component mounts the DOM
    //fetch is async and returns a promise
    //then() fires a callback function after the fetch is done
    //response.json is also async and returns a promise
    this.loadJugadores(1, 20)
	},
}
</script>

<style scoped>

</style>