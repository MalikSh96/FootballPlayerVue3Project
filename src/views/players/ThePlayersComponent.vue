<template>
  <h1 class="text-2xl font-bold text-indigo-600">The Players</h1>
  <div class="relative overflow-x-auto shadow-md sm:rounded-lg">
    <label class="pr-2 italic">Row limit</label>
    <select v-model="perPage" class="border-gray-300 focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 shadow-sm
                                   border-black-100 placeholder-blueGray-300 text-black bg-white rounded text-sm shadow
                                   focus:outline-none focus:ring-indigo-600 ease-linear transition-all duration-150">
      <option v-for="(page, key) in players.items.length" :key="key"> {{ page }}</option>
    </select>
    
    <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
      <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
        <tr>
          <th scope="col" class="px-6 py-3">
            Name
          </th>
          <th scope="col" class="px-6 py-3">
            Age
          </th>
          <th scope="col" class="px-6 py-3">
            Club
          </th>
          <th scope="col" class="px-6 py-3">
            Details...
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(player) in displayedPlayers" :key="player.id" 
            class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
          <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
            {{ player.name }}
          </th>
          <td class="px-6 py-3">
            {{ player.age }}
          </td>
          <td class="px-6 py-3" v-for="(c, key) in club" :key="key" v-bind="player.club">
            {{ c.name }}
            <!-- {{ player.club }} -->
            <!-- <button type="button" @click="getPlayerClub(player.club)"> 
              click
            </button> -->
            <!-- <PlayerClubComponent :playerClubId="club" /> -->
              
          </td>
          <td class="px-6 py-3">
            <button type="button" class="btn btn-primary" @click="openModal(player.id)">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path d="M10 12a2 2 0 100-4 2 2 0 000 4z" />
                <path fill-rule="evenodd" d="M.458 10C1.732 5.943 5.522 3 10 3s8.268 2.943 9.542 7c-1.274
                      4.057-5.064 7-9.542 7S1.732 14.057.458 10zM14 10a4 4 0 11-8 0 4 4 0 018 0z" clip-rule="evenodd" />
              </svg>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <!--modal-->
    <transition name="fade" appear>
      <div class="modal-overlay" v-if="modalIsVisible" @click="closeModal()"></div>
    </transition>
    <transition name="slide" appear>
      <div class="modal relative bg-white rounded-lg shadow dark:bg-gray-700" v-if="modalIsVisible">
        <PlayerModalComponent :showPlayer="showPlayer"/>
        <button type="button"  class="text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg 
          text-sm p-1.5 ml-auto inline-flex items-center dark:hover:bg-gray-600 dark:hover:text-white" 
          @click="closeModal()"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
          </svg>
        </button>
      </div>
    </transition>

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
            @click="page = pageNumber; loadPlayers(page, limit)"> {{ pageNumber }} 
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
import PlayerModalComponent from '@/components/helpers/PlayerModalComponent.vue'
// import PlayerClubComponent from '@/components/helpers/PlayerClubComponent.vue'
// import reactive from 'vue'

export default {
  components: {
    PlayerModalComponent,
    // PlayerClubComponent
  },
  data() {
    return {
			page: 1,
			perPage: 20,
			pages: [],		
      players: [],
      club: {},
      modalIsVisible: false,
      showPlayer: {},
      limit: 20
    }
  }, 
  methods: {
    showModal (value) {
      console.log('value', value)
      console.log('bool before', this.modalIsVisible)
      this.modalIsVisible = value
      console.log('bool after', this.modalIsVisible)
    },
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
    openModal(id) {
      this.modalIsVisible = true
      fetch('https://futdb.app/api/players/' + id, {
        method: "GET",
        headers: {
          'content-type': 'application/json',
          'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
        }
      })
      .then((response) => response.json())
      .then(data => (this.showPlayer = data/*, console.log('player', this.player)*/))
      .catch(err => console.log(err.message))
            
      console.log('after', this.showPlayer) 
    },
    closeModal() {
      this.modalIsVisible = false
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
    // fetch('https://futdb.app/api/players?limit=20', {
    //   method: "GET",
    //   headers: {
    //     'content-type': 'application/json',
    //     'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
    //   }
    // })
    // .then((response) => response.json())
    // .then(data => (this.players = data))
    // .catch(err => console.log(err.message))
    console.log('page ' + this.page + ' and limit ' + this.limit)
    this.loadPlayers(this.page, this.limit)
	},
  mounted() {
    if(localStorage.perPage) {
      this.perPage = localStorage.perPage
    }

    const playersArr = Object.values(this.players.items)
    for(let i = 0; i < playersArr.length; i++)
    {
      // console.log('ttttt', playersArr[i].club)
      this.getPlayerClub(playersArr[i].club)
    }
  },
  // async mounted() {
  //   // const playersArr = Object.values(this.players.items)
  //   // for(let i = 0; i < playersArr.length; i++)
  //   // {
  //   //   // console.log('ttttt', playersArr[i].club)
  //   //   await this.getPlayerClub(playersArr[i].club)
  //   // }
  // },
}
</script>

<style scoped>
button.page-link {
	display: inline-block;
}
button.page-link {
    font-size: 20px;
    color: #29b3ed;
    font-weight: 500;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 98;
  background-color: rgba(0, 0, 0, 0.3);
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 99;

  background-color: #FFF;
  border-radius: 16px;

  padding: 25px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity .5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.slide-enter-active,
.slide-leave-active {
  transition: transform .5s;
}

.slide-enter,
.slide-leave-to {
  transform: translateY(-50%) translateX(100vw);
}
</style>

