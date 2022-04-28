<template>
  <h1 class="text-2xl font-bold text-indigo-600">The Players</h1>
  <div class="relative overflow-x-auto shadow-md sm:rounded-lg">
    <label class="pr-2 italic">Row limit</label>
    <select v-model="perPage" class="border-gray-300 focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 shadow-sm
                                   border-black-100 placeholder-blueGray-300 text-black bg-white rounded text-sm shadow
                                   focus:outline-none focus:ring-indigo-600 ease-linear transition-all duration-150">
      <option v-for="(page, key) in players.items.length" :key="key"> {{ page }}</option>
    </select>
    
    <table-component :PlayersData="displayedPlayers"/>

    <nav aria-label="Page navigation">
      <ul class="inline-flex space-x-2">
        <li class="page-item">
          <button type="button" 
            class="flex items-center justify-center w-10 h-10 text-indigo-600 
            transition-colors duration-150 rounded-full focus:shadow-outline hover:bg-indigo-100" 
            v-if="page != 1" @click="page--"
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
            @click="page = pageNumber; /*loadPlayers(page, limit)*/"> {{ pageNumber }} 
          </button>
        </li>
        <li class="page-item">
          <button type="button" @click="page++" v-if="page < pages.length" class="flex items-center justify-center w-10 h-10 text-indigo-600 
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
      </ul>
		</nav>	
  </div>
</template>

<script>
import TableComponent from '@/components/helpers/TableComponent.vue'

export default {
  components: {
    TableComponent
  },
  data() {
    return {
			page: 1,
			perPage: 20,
			pages: [],		
      players: [],
      club: {},
      limit: 20
    }
  }, 
  methods: {
    loadPlayers(currentPage, limitation) {
      console.log('BEFORE FETCH page ' + this.page + ' and limit ' + this.limit)
      fetch(`https://futdb.app/api/players?page=${currentPage}&limit=${limitation}`, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.players = data))
      .catch(err => console.log(err.message))
      console.log('fetched players', this.players)
      
    },
    getPlayerClub (clubId) {
      fetch('https://futdb.app/api/clubs/' + clubId, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.club = data, console.log('club', this.club)))
      .catch(err => console.log(err.message))
    },
    setPages () {
      if(localStorage.perPage) {
        this.perPage = localStorage.perPage
      }
      //math ceil rounds a number up to the next largest integer
			// let numberOfPages = Math.ceil(Object.values(this.players.items).length / this.perPage);
      console.log('pushing p')
			for (let index = 1; index <= this.players.page_total; index++) {
				this.pages.push(index);
			}
		},
		paginate (players) {
			let page = this.page;
			let perPage = this.perPage;
      // console.log('what is pag', perPage)
			let from = (page * perPage) - perPage;
			let to = (page * perPage);
			return players.slice(from, to);
		},

  },
  computed: {
		displayedPlayers () {
      //Converting object to array
      console.log('page total', this.players.page_total)
			return this.paginate(Object.values(this.players.items));
		},
	},
	watch: { //watch triggers a function whenever a reactive property changes
		players () {
      console.log('called', this.perPage)
			this.setPages();
		},
    perPage(newValue) {
      localStorage.perPage = newValue;
    },
    page (newPage) {
      console.log('new page', newPage)
      this.page = newPage
    },
    limit (newLimit) {
      console.log('new limit', newLimit)
      this.limit = newLimit
    }
	},
	created(){
    //To get the data at a "popular" point is to get it inside either the created or mounted lifecycle hook
    //A vue component have lifecycle hooks which fires at different points of their lifecycle
    //The mounted lifecycle hook fires when the component mounts the DOM
    //fetch is async and returns a promise
    //then() fires a callback function after the fetch is done
    //response.json is also async and returns a promise
    console.log('page ' + this.page + ' and limit ' + this.limit)
    this.loadPlayers(this.page, this.limit)
	},
  mounted() {
    if(localStorage.perPage) {
      this.perPage = localStorage.perPage
    }

    // const playersArr = Object.values(this.players.items)
    // for(let i = 0; i < playersArr.length; i++)
    // {
    //   // console.log('ttttt', playersArr[i].club)
    //   this.getPlayerClub(playersArr[i].club)
    // }
  },
}
</script>

<style scoped>

</style>