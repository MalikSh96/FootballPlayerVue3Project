<template>
  <div class="modal-backdrop">
    <div class="modal">
      <header class="modal-header">
        <slot name="header">
          <p class="font-bold text-white">FILTER THE PLAYERS TABLE</p>
        </slot>
        <button type="button" class="btn-close" @click="close">
          x
        </button>
      </header>

      <section class="modal-body">
        <slot name="body">
          <p class="font-bold text-left text-white">Filter by players age:</p>
        </slot>
        <div>
          <p class="text-white">Filter by age:</p>
          <select class="border-2 mb-5 rounded h-10 p-1" 
              @input="$emit('update:operator', $event.target.value)">
            <option value="" selected>None</option>
            <option value="=">Equals to</option> <!-- THIS OPTION IS NOT WORKING RN-->
            <option value=">">Bigger than</option>
            <option value="<">Lesser than</option>
          </select>
          <input :value="playerAge" @input="$emit('update:playerAge', $event.target.value)"
            class="border-2 mb-5 rounded h-10 p-2" placeholder="Age" type="number">
        </div>

        <slot name="body">
          <p class="font-bold text-left text-white">Filter by name letter:</p>
        </slot>
        <div class="letters-list">
          <div class="letters-wrap" v-for="letter in letters" :key="letter">
            <div v-if="isNameFilteredByLetter(letter)" class="has-data font-normal hover:font-bold">
              <input :id="letter" type="radio" :value="letter" v-model="lettersHandle"
                @input="$emit('update:nameFiltering', $event.target.value)">
              <label :for="letter">{{ letter }}</label>
            </div>
            <div v-else class="">{{ letter }}</div>
          </div>
        </div>
        <div>
          <button @click="rowsResetter()">
            <p class="font-normal hover:font-bold text-white">RESET THE NAME FILTER</p>
          </button>
        </div>

        <slot name="body">
          <p class="font-bold text-left text-white">Filter by club letter:</p>
        </slot>
        <div class="letters-list">
          <div class="letters-wrap" v-for="letter in letters" :key="letter">
            <div v-if="isClubFilteredByLetter(letter)" class="has-data font-normal hover:font-bold">
              <input :id="letter" type="radio" :value="letter" v-model="clubLetterHandle"
                @input="$emit('update:clubFiltering', $event.target.value)">
              <label :for="letter">{{ letter }}</label>
            </div>
            <div v-else class="">{{ letter }}</div>
          </div>
        </div>
        <div>
          <button @click="clubRowsResetter()">
            <p class="font-normal hover:font-bold text-white">RESET THE CLUB FILTER</p>
          </button>
        </div>
       </section>

      <footer class="modal-footer">
        <button type="button" class="btn-green hover:bg-[#1F2739]" @click="close">
          Close
        </button>
      </footer>
    </div>
  </div>
</template>
<script>
  export default {
    props: {
        playerAge: String,
        operator: String,
        rows: Array,
        nameFiltering: String,
        clubFiltering: String
    },
    data() {
      return {
        lettersHandle: '',
        clubLetterHandle: '',
      }
    },
    emits: ['update:playerAge', 'update:operator', 'update:nameFiltering', 'update:clubFiltering', 'close'],
    computed: {
      letters() { //generates letters from A-Z
        let letters = []
        for(let i = "A".charCodeAt(0); i <= "Z".charCodeAt(0); i++) {
          letters.push(String.fromCharCode([i]))
        }
        return letters
      },
    },
    watch: {
      nameFiltering(value) {
        this.lettersHandle = value
      },
      clubFiltering(value) {
        this.clubLetterHandle = value
      },
    },
    methods: {
      rowsResetter() {
        this.$emit('update:nameFiltering')
      },
      clubRowsResetter() {
        this.$emit('update:clubFiltering')
      },
      isNameFilteredByLetter(letter) { //highlights letter if exists
        return this.rows.some(player => player.name.startsWith(letter))
      },
      isClubFilteredByLetter(letter) { //highlights letter if exists
        return this.rows.some(player => player.club.startsWith(letter))
      },
      close() {
        this.$emit('close');
      },
    },
  };
</script>

<style scoped>
  .modal-backdrop {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.3);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal {
    background: #FB667A;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
  }

  .modal-header,
  .modal-footer {
    padding: 15px;
    display: flex;
  }

  .modal-header {
    position: relative;
    border-bottom: 1px solid #39A0ED;
    color: white;
    justify-content: space-between;
  }

  .modal-footer {
    border-top: 1px solid #39A0ED;
    flex-direction: column;
    justify-content: flex-end;
  }

  .modal-body {
    position: relative;
    padding: 20px 10px;
  }

  .btn-close {
    position: absolute;
    top: 0;
    right: 0;
    border: none;
    font-size: 20px;
    padding: 10px;
    cursor: pointer;
    font-weight: bold;
    color: white;
    background: transparent;
  }

  .btn-green {
    color: white;
    background: #39A0ED;
    border: 1px solid #39A0ED;
    border-radius: 2px;
  }

  /* LETTERS */
.letters-list {
    text-align: center;
    padding: 10px 0;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
}

.letters-wrap {
    padding: 0 5px;
}
.letters-wrap input {
    display: none;
}
.letters-wrap label {
    cursor: pointer;
    color: white;
}

.list-inner {
    
}
/* LETTERS */
</style>