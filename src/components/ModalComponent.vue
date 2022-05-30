<template>
  <div class="modal-backdrop">
    <div class="modal">
      <header class="modal-header">
        <slot name="header">
          <p class="font-bold">FILTER THE PLAYERS TABLE</p>
        </slot>
        <button type="button" class="btn-close" @click="close">
          x
        </button>
      </header>

      <section class="modal-body">
        <slot name="body">
          Filter by player age
        </slot>
        <!--TODO: HAVE FILTER METHOD DO ITS JOB HERE-->
        <div>
            <p class="text-white">Filter by age:</p>
            <select class="border-2 mb-5 rounded h-10 p-1" 
                    @input="$emit('update:searchOperator', $event.target.value)">
                <option value="" selected>None</option>
                <option value="=">Equals to</option> <!-- THIS OPTION IS NOT WORKING RN-->
                <option value=">">Bigger than</option>
                <option value="<">Lesser than</option>
            </select>
            <input :value="playerAge" @input="$emit('update:playerAge', $event.target.value)"
                class="border-2 mb-5 rounded h-10 p-2" placeholder="Age" type="number">
        </div>
        <slot name="body">
          Filter by player name
        </slot>
        <!-- <div class="letters-list">
            <p class="text-white">Filter by start letter:</p>
            <div class="letters-wrap" v-for="letter in letters" :key="letter">
                <div v-if="isFilteredByLetter(letter)" class="has-data font-normal hover:font-bold">
                    <input :id="letter" type="radio" :value="letter" v-model="lettersFilter">
                    <label :for="letter">{{ letter }}</label>
                </div>
                <div v-else class="text-white">{{ letter }}</div>
            </div>
        </div>
        <div>
            <button @click="rowsResetter()">
                <p class="font-normal hover:font-bold text-red-600">RESET THE LETTER FILTER</p>
            </button>
        </div> -->
       </section>

      <!-- <footer class="modal-footer">
        <slot name="footer">
          This is the default footer!
        </slot>
        <button
          type="button"
          class="btn-green"
          @click="close"
        >
          Close Modal
        </button>
      </footer> -->
    </div>
  </div>
</template>
<script>
  export default {
    props: {
        playerAge: String,
        searchOperator: String
    },
    emits: ['update:playerAge', 'update:searchOperator'],
    methods: {
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
    background: #FFFFFF;
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
    border-bottom: 1px solid #eeeeee;
    color: #4AAE9B;
    justify-content: space-between;
  }

  .modal-footer {
    border-top: 1px solid #eeeeee;
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
    color: #4AAE9B;
    background: transparent;
  }

  .btn-green {
    color: white;
    background: #4AAE9B;
    border: 1px solid #4AAE9B;
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
    color: #0de358;
}

.list-inner {
    
}
/* LETTERS */
</style>