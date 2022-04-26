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
                    scope="col" class="px-6 py-3" @click="sortRecords(index)">
                        {{column}}
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
                    <th scope="row" class="px-6 py-4 font-medium text-gray-900 dark:text-white whitespace-nowrap">
                        {{ row.details }}
                    </th>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
const performSearch = (rows, term) => {
    console.log('checking rows again', rows)
    const results = rows.filter(
        row => row.join(" ").toLowerCase().includes(term.toLowerCase())
    )
    return results;
}

export default {
    props: {
        PlayersData: Object
    },
    data () {
        return {
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
        }
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