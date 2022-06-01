<template>
    <div>
        <button class="btn hover:font-bold hover:dark:text-white dark:text-[#FB667A]" type="button" @click="openExtraModal">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4" />
            </svg>
        </button>

        <modal-component v-show="isExtraModalVisible" @close="closeExtraModal" :rows="rows" 
            :playerAge="age" @update:playerAge="newValue => age = newValue"
            :operator="searchOperator" @update:operator="newValue => searchOperator = newValue"
            :nameFiltering="nameFilter" @update:nameFiltering="newValue => nameFilter = newValue"
            :clubFiltering="clubFilter" @update:clubFiltering="newValue => clubFilter = newValue"
        />
    </div>

    <div>
        <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
            <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-[#39A0ED] dark:text-white">
                <tr>
                    <th v-for="key in columns" :key="key" @click="sortBy(key)" :class="{ active: sortKey == key }" class="px-6 py-3 cursor-pointer">
                        {{ capitalize(key) }}
                        <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
                        </span>
                    </th>
                    <th class="px-6 py-3">
                        DETAILS
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="entry in filteredData" :key="entry" class="bg-white border-b dark:bg-[#1F2739] dark:border-[#39A0ED] dark:text-white">
                    <td v-for="key in columns" :key="key" class="px-6 py-3">
                        {{entry[key]}}
                    </td>
                    <td class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        <button type="button" class="btn btn-primary" @click="openModal(entry.id)">
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
            <div class="modal relative bg-white rounded-lg shadow dark:bg-[#FB667A]" v-if="modalIsVisible">
                <PlayerModalComponent :showPlayer="showPlayer"/>
                <button type="button" class="text-white bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg 
                text-sm p-1.5 ml-auto inline-flex items-center dark:hover:bg-[#39A0ED] dark:hover:text-white" 
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
import ModalComponent from '../ModalComponent.vue'

export default {
    components: {
        PlayerModalComponent,
        ModalComponent,
    },
    props: {
        playersData: Object,
        filterKey: String,
        columns: Array,
    }, 
    data() {
        return {
            modalIsVisible: false,
            isExtraModalVisible: false,
            showPlayer: {},
            rows: [],
            sortKey: '',
            sortOrders: this.columns.reduce((o, key) => ((o[key] = 1), o), {}),
            searchOperator: '',
            club: '',
            age: '',
            nameFilter: '',
            clubFilter: '', 
        }
    },
    computed: {
        filteredData() {
            const sortKey = this.sortKey
            const filterKey = this.filterKey && this.filterKey.toLowerCase()
            const order = this.sortOrders[sortKey] || 1
            let data = this.rows
            if(filterKey) { 
                data = data.filter((row) => {
                    return Object.keys(row).some((key) => {
                        return String(row[key]).toLowerCase().indexOf(filterKey) > -1
                    })
                })
            }
            if(sortKey) { 
                data = data.slice().sort((a, b) => {
                    a = a[sortKey]
                    b = b[sortKey]
                    return (a === b ? 0 : a > b ? 1 : -1) * order
                })
            }
            if(this.nameFilter) {
                return data.filter(this.filterByAge).filter((item) => {
                    return item.name.toLowerCase().startsWith(this.nameFilter.toLowerCase());
                })
            } 
            if(this.clubFilter) {
                return data.filter(this.filterByAge).filter((item) => {
                    return item.club.toLowerCase().startsWith(this.clubFilter.toLowerCase());
                })
            } 
            return data.filter(this.filterByAge)
        },
    },
    watch: {
        playersData: async function() {
            if(this.playersData) {
                let id, name, age, club
                for(let data of this.playersData) {
                    id = data.id
                    name = data.name
                    age = data.age
                    await this.getClub(data.club)
                    club = this.club
                    this.rows.push({id, name, age, club})
                }
            }
        },
        sortKey(newKey) {
            console.log('SORT KEY', newKey)
            localStorage.sortKey = newKey
        },
    },
    methods: {
        sortBy(key) {
            this.sortKey = key
            this.sortOrders[key] = this.sortOrders[key] * -1
        },
        capitalize(str) {
            return str.charAt(0).toUpperCase() + str.slice(1)
        },
        filterByAge(data) {
            // no operator selected or no age typed, don't filter : 
            if(this.searchOperator.length === 0 || this.age.length === 0) {
                return true;
            }
            if(this.searchOperator === '>') {
                return (data.age > this.age); 
            } else  if(this.searchOperator === '<') {
                return (data.age < this.age);
            } else if(this.searchOperator === '=') {
                return (data.age === this.age)
            }
        },
        async getClub(clubId) {
            await fetch('https://futdb.app/api/clubs/' + clubId, {
                method: "GET",
                headers: {
                'content-type': 'application/json',
                'X-AUTH-TOKEN': process.env.VUE_APP_FUT_API_KEY 
                }
            })
            .then((response) => response.json())
            .then(data => (this.club = data.item.name))
            .catch(err => console.log(err.message))
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
            .then(data => (this.showPlayer = data))
            .catch(err => console.log(err.message))
        },
        closeModal() {
            this.modalIsVisible = false
        },
        openExtraModal() {
            console.log('EXTRA MODAL')
            this.isExtraModalVisible = true
        },
        closeExtraModal() {
            this.isExtraModalVisible = false
        }
    },
    created () {
        if(localStorage.sortKey) {
            //sorts ascending only on each page refresh/page change, for some reason
            this.sortBy(localStorage.sortKey)
        }
        console.log('CREATED SORT KEY', this.sortOrders[this.sortKey])
    }
}
</script>

<style scoped>
/* MODAL */
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
/* MODAL */

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
}

.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
  opacity: 0.66;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #fff;
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #fff;
}
</style>