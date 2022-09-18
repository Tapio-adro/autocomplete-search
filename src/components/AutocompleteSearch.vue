<template>
  <input 
    type="text"
    @input="updateSearch"
    ref="input"
    placeholder="city"
    v-model="inputValue"
    @focus="isInputFocused = true"
    @blur="blurInput()"
  >
  <div 
    class="search_results"
    v-show="isInputFocused && inputValue.length"
  >
    <div 
      v-for="(city, index) in searchResult.slice(0, 15)" :key="index"
      class="result"
      :class="{active: chosenResultId == index}"
      @click="handleItemClick(index)"
    > 
      <b>{{ city.name.slice(0, inputValue.length) }}</b>{{city.name.slice(inputValue.length, city.name.length)}}
    </div>
  </div>
  <div 
    class="result_item"
    v-if="chosenResult !== null && !isInputFocused"
  >
    <h3 class="header">{{ chosenResult.name }}</h3>
    <ul>
      <li 
        class="item_data"
        v-for="(item, index) in Object.entries(chosenResult)" :key="index"
      > 
        {{item[0] + ': ' + item[1]}}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'AutocompleteSearch',
  props: {
    citiesArray: Object
  },
  data() {
    return {
      searchResult: [],
      inputValueBefore: '',
      inputValue: '',
      inputsHistory: [''],
      isInputFocused: true,
      chosenResultId: null,
      chosenResult: null
    }
  },
  methods: {
    updateSearch() {
      if (this.inputValue.length == 0) {
        this.searchResult = [];
        this.chosenResultId == null;
        return;
      }
      if (this.inputValue.length - this.inputValueBefore.length == 1 && this.inputValue.length != 1) {
        console.log('now');
        this.searchResult = this.searchResult.filter((city) => {
          return city.name.toLowerCase().startsWith(this.inputValue.toLowerCase())
        })
      } else {
        this.searchResult = []; 
        for (let city of this.citiesArray) {
          if (city.name.toLowerCase().startsWith(this.$refs.input.value.toLowerCase())) {
            if (this.$refs.input.value == '') return;
            this.searchResult.push(city);
          }
        }
      }
      this.chosenResultId = 0;
    },
    handleItemClick(index) {
      this.chosenResult = this.searchResult[index];
      this.chosenResultId = index;
    },
    blurInput() {
      setTimeout(() => {
        this.isInputFocused = false
      }, 110)
    }
  },
  mounted() {
    // this.updateSearch()
    this.$refs.input.focus();
    // this.chosenResult = this.searchResult[this.chosenResultId];
    // this.$refs.input.blur();
  },
  created() {
    let that = this;
    document.onkeydown = function (e) {
      if (e.keyCode == 38 || e.keyCode == 40) e.preventDefault();

      let resultsLength = that.searchResult.length;
      let id = that.chosenResultId;
      if (e.keyCode == 38) {
        that.chosenResultId = id > 0 ? id - 1 : resultsLength - 1; 
      } else if (e.keyCode == 40) {
        that.chosenResultId = id < resultsLength - 1 ? id + 1 : 0;
      }

      if (e.code == 'Enter' && that.chosenResultId !== null && that.isInputFocused) {
        that.chosenResult = that.searchResult[that.chosenResultId];
        that.$refs.input.blur();
      } 
      if (e.code == 'Escape' && that.chosenResult !== null && !that.isInputFocused) {
        that.chosenResult = null;
        that.$refs.input.focus();
      }
    }
  },
  watch: {
    inputValue() {
      let inputValue = this.inputValue;
      this.inputsHistory.push(inputValue);
      if (this.inputsHistory.length > 2) {
        this.inputsHistory.shift();
        this.inputValueBefore = this.inputsHistory[0];
      }
    }
  }
}
</script>