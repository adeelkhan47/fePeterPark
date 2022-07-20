
<template>
  <div id="app">
    <AppHeader />
    <b-form-input v-model="search" placeholder="Search by License Plate or Owner"></b-form-input>
    <LicensePlate v_if="plates.length" :plates="plates" :search="search"/>

  </div>
</template>

<script>
import LicensePlate from './components/LicensePlate.vue'
import AppHeader from './components/AppHeader.vue'

const BACKEND = "http://127.0.0.1:5001/api/v1/plate"



export default {
  name: 'App',
  components: {
    AppHeader,
    LicensePlate
  },
  data() {
    return {
      plates: [],
      total: 0,
      search : ""
    }
  },
  methods : {
    fetchData() {
      if(this.search)
      
      fetch(BACKEND+"?plate:like="+this.search+"&owner_name:like="+this.search, {
        method: 'get'
      })
        .then((response) => {
          console.log(response)
          if (response.status === 200)
            return response.json()
        })
        .then((jsonData) => {
          console.log(jsonData)
          this.plates = jsonData.objects
          this.total = jsonData.total_rows
        })
      else
        fetch(BACKEND, {
          method: 'get'
        })
          .then((response) => {
            console.log(response)
            if (response.status === 200)
              return response.json()
          })
          .then((jsonData) => {
            console.log(jsonData)
            this.plates = jsonData.objects
            this.total = jsonData.total_rows
          })
  }
  },
  watch:{
    search() {
          this.fetchData()
    }
  },
  mounted: function() {
    this.fetchData()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
