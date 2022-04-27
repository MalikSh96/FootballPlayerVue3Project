<template>
<!-- {{PlayersData.items[0]}} -->
    <div class="p-10">
        <div>
            <input type="text" class="border-2 mb-5 rounded h-10 p-2" 
                placeholder="Search records" @input="onSearch"/>
        </div>

        <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
            <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
                <tr>
                    <th v-for="(column, index) in columns" v-bind:key="index" 
                    scope="col" class="px-6 py-3 cursor-pointer" @click="sortRecords(index)">
                        {{column}}

                        <div v-if="sortDirection == null || sortDirection === 'desc'">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path d="M3 3a1 1 0 000 2h11a1 1 0 100-2H3zM3 7a1 1 0 000 2h5a1 1 0 000-2H3zM3 11a1 1 0 100 2h4a1 1 0 100-2H3zM13 16a1 1 0 102 0v-5.586l1.293 1.293a1 1 0 001.414-1.414l-3-3a1 1 0 00-1.414 0l-3 3a1 1 0 101.414 1.414L13 10.414V16z" />
                            </svg>
                        </div>
                        <div v-else-if="sortDirection === 'asc'">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path d="M3 3a1 1 0 000 2h11a1 1 0 100-2H3zM3 7a1 1 0 000 2h7a1 1 0 100-2H3zM3 11a1 1 0 100 2h4a1 1 0 100-2H3zM15 8a1 1 0 10-2 0v5.586l-1.293-1.293a1 1 0 00-1.414 1.414l3 3a1 1 0 001.414 0l3-3a1 1 0 00-1.414-1.414L15 13.586V8z" />
                            </svg>
                        </div>
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(row, index) in rows" v-bind:key="index" 
                    class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
                    <!-- <td v-for="(rowItem, itemIndex) in row" v-bind:key="itemIndex" class="px-6 py-3">
                        {{rowItem}}
                    </td> -->
                    <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        {{ row.name }}
                    </th>
                    <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        {{ row.age }}
                    </th>
                    <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        {{ row.club }}
                    </th>
                    <!-- <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        {{ row.details }}
                    </th> -->
                    <td class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        <button type="button" class="btn btn-primary" @click="openModal(row.id)">
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
        <!--modal-->
    </div>
</template>

<script>
import PlayerModalComponent from '@/components/helpers/PlayerModalComponent.vue'

const performSearch = (rows, term) => {
    console.log('checking rows again', rows)
    const results = rows.filter(
        row => row.join(" ").toLowerCase().includes(term.toLowerCase())
    )
    return results;
}

export default {
    components: {
        PlayerModalComponent
    },
    props: {
        PlayersData: Object,
        
    },
    data () {
        return {
            modalIsVisible: false,
            showPlayer: {},
            term: '',
            // rawRows: [
            //     [
            //     "Manoj", "24", "Software Developer", "1997"
            //     ],
            //     [
            //     "John", "26", "Lawyer", "1995"
            //     ],
            //     [
            //     "Lily", "34", "Saleswoman", "1987"
            //     ],
            //     [
            //     "Rachel", "34", "Saleswoman", "1987"
            //     ],
            //     [
            //     "Ross", "34", "Barber", "1987"
            //     ],
            //     [
            //     "Chandler", "30", "Salesman", "1991"
            //     ]
            // ],
            rows: [],
            columns: [
                'Name',
                'Age',
                'Club',
                'Details'
            ],
            sortIndex: null,
            sortDirection: null
        }
    },
    methods: {
        sortRecords (index) {
            if (this.sortIndex === index) {
                switch (this.sortDirection) {
                    case null:
                        this.sortDirection = 'asc';
                        break;
                    case 'asc':
                        this.sortDirection = 'desc';
                        break;
                    case 'desc':
                        this.sortDirection = null;
                        break;
                }
            } else {
                this.sortDirection = 'asc'
            }
            this.sortIndex = index;
            if (!this.sortDirection) {
                this.rows = performSearch(Object.values(this.PlayersData.items), this.term);
                return;
            }
            this.rows = this.rows.sort(
                (rowA, rowB) => {
                    if (this.sortDirection === 'desc') {
                        return Object.values(rowB)[2].localeCompare(Object.values(rowA)[2])
                    }
                    return Object.values(rowA)[2].localeCompare(Object.values(rowB)[2])
                }
            )
        },
        onSearch (e) {
            this.term = e.target.value;
            this.rows = performSearch(Object.values(this.PlayersData.items), this.term);
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
    mounted () {
        // this.rows = [...this.rawRows];
        let p = [];
        // console.log('data of players before', this.PlayersData.items)
        p = Object.values(this.PlayersData.items)
        // console.log('data of players before', p)
        this.rows = [...p]
        console.log('rows data', this.rows)
    }
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