<template>
{{newPlayers}}
    <table-component :PlayersData="newPlayers.items"/>
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
                v-for="(pageNumber, key) in testPages" :key="key" 
                @click="page = pageNumber; getPlayers(page);"> {{ pageNumber }} 
                </button>
            </li>
            <li class="page-item">
                <button type="button" @click="page++" v-if="page < testPages.length" class="flex items-center justify-center w-10 h-10 text-indigo-600 
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
          <p class="py-2 italic">|| Total pages: {{ testPages.length }}</p>
        </ul>
      </nav>	
</template>

<script>
import TableComponent from './TableComponent.vue'

export default {
  components: { TableComponent },
    
    setup() {
        
    },
    props: {
        PlayersData: Object,
    },
    data() {
        return {
            page: 1,
            pagesTotal: null,
            pages: [],
            perPage: 20,
            testPages: [1,2,3,4,5],
            newPlayers: [],
            prevPlayers: this.PlayersData
        }
    },
    methods: {
        async getPagesTotal() {
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
            console.log('BEFORE FETCH GET PLAYERS ' + page)
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
            console.log('players in pagination', Object.values(this.newPlayers.items))
        },
        setPages () {
            if(localStorage.perPage) {
                this.perPage = localStorage.perPage
            }
            // console.log('pushing p')
            for (let index = 1; index <= this.pagesTotal; index++) {
                this.pages.push(index);
            }
		},
    },
    watch: {
        page(newValue, oldValue) {
            console.log('new value', newValue)
            console.log('old value', oldValue)
        },
        newPlayers() {
            // console.log('what is new page value?', this.page)
            console.log('what is the old player data', this.newPlayers.items)
            Object.values(this.newPlayers.items)
        }
    },
    async mounted () {
        console.log('prevPlayers before create pagination', this.prevPlayers)
        this.newPlayers = this.prevPlayers
        console.log('newPlayers before create pagination AGAIN', this.newPlayers)
    }
}
</script>
