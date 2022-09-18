<template>
  <input 
    type="text"
    @input="updateSearch"
    ref="input"
    v-model="inputValue"
  >
  <div v-for="(city, index) in searchResult" :key="index"> 
    <b>{{ city.slice(0, inputValue.length) }}</b>{{city.slice(inputValue.length, city.length - 1)}}
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
      inputsHistory: ['']
    }
  },
  methods: {
    updateSearch() {
      if (this.inputValue.length - this.inputValueBefore.length == 1 && this.inputValue.length != 1) {
        this.searchResult = this.searchResult.filter((city) => {
          return city.toLowerCase().startsWith(this.inputValue.toLowerCase())
        })
      } else {
        this.searchResult = []; 
        for (let city of this.citiesArray) {
          if (city.name.toLowerCase().startsWith(this.$refs.input.value.toLowerCase())) {
            if (this.$refs.input.value == '') return;
            this.searchResult.push(city.name);
          }
        }
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