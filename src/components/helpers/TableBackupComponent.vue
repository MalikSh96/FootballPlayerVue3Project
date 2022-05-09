<template>
    <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
            <tr>
                <th v-for="key in columns" :key="key" @click="sortBy(key)" :class="{ active: sortKey == key }" class="px-6 py-3 cursoer-pointer">
                    {{ capitalize(key) }}
                    <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
                    </span>
                </th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="entry in filteredData" :key="entry" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
                <td v-for="key in columns" :key="key" class="px-6 py-3">
                    {{entry[key]}}
                </td>
            </tr>
        </tbody>
  </table>
</template>

<script>
export default {
    props: {
        playersData: Object,
        filterKey: String,
        columns: Array,
    }, 
    data() {
        return {
            rows: [],
            sortKey: '',
            sortOrders: this.columns.reduce((o, key) => ((o[key] = 1), o), {}),
            club: '',
        }
    },
    computed: {
        filteredData() {
            const sortKey = this.sortKey
            const filterKey = this.filterKey && this.filterKey.toLowerCase()
            const order = this.sortOrders[sortKey] || 1
            let data = this.rows
            if (filterKey) { 
                data = data.filter((row) => {
                    return Object.keys(row).some((key) => {
                        return String(row[key]).toLowerCase().indexOf(filterKey) > -1
                    })
                })
            }
            if (sortKey) { 
                console.log('FILTERED ACCESSED NOW', data)
                data = data.slice().sort((a, b) => {
                    a = a[sortKey]
                    b = b[sortKey]
                    return (a === b ? 0 : a > b ? 1 : -1) * order
                })
            }
            return data
        }
    },
    methods: {
        sortBy(key) {
            this.sortKey = key
            this.sortOrders[key] = this.sortOrders[key] * -1

        },
        capitalize(str) {
            return str.charAt(0).toUpperCase() + str.slice(1)
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
            .then(data => (this.club = data.item.name/*, console.log('clubs', this.club)*/))
            .catch(err => console.log(err.message))
        },
    },
    beforeMount () {
        console.log('CHILD playersData CHECK', this.playersData)
        let id, name, age
        for(let data of this.playersData) {
            id = data.id
            name = data.name
            age = data.age
            // this.getClub(data.club)
            // club = this.club
            // console.log('WHAT IS CLUB', club)
            // this.rawRows.push([id, name, age, club])
            this.rows.push({id, name, age})
        }
    },
}
</script>


<style>
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
