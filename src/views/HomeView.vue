<template>
  <main v-if="!loading" class="home" >
    <DataTitle :title="title" :dataDate="dataDate"/>
    <DataBox :stats="stats" />
    <CountrySelector :countries="countries" @newcountry="setNewCountry"/>
    <button class="mt-10 pl-10 pr-10 pt-4 pb-4 text-white rounded bg-green-400 font-bold hover:bg-green-700" v-if="title != 'Global'" @click="resetToGlobal">Reset</button>
  </main>
  <main v-else class="flex flex-col align-center justify-center text-center">
      <div class="text-gray-500 text-bold text-3xl mt-10 mb-6">
        Fetching Data
      </div>
      <img :src="loadingImage" alt="loading image" class="w-24 m-auto">
  </main>
</template>

<script>
// @ is an alias to /src
import DataTitle from '@/components/DataTitle.vue'
import DataBox from '@/components/DataBox.vue'
import CountrySelector from '@/components/CountrySelector.vue'

export default {
  name: 'HomeView',
  components: {
    DataTitle,
    DataBox,
    CountrySelector
  },
  data(){
    return {
      loading: true,
      title: 'Global', 
      countries:[],
      dataDate: '',
      loadingImage: require('../assets/hourglass.gif'),
      stats: {}
    }
  },
  methods: {
    async fetchCovidData(){
        const res = await fetch('https://api.covid19api.com/summary')
        const data = await res.json()
        return data
    },

    setNewCountry(country_id){
      const country = this.countries.find((item)=> item.ID === country_id)
      this.title = country.Country
      this.stats = country
    },

    async resetToGlobal(){
    this.loading = true
    const data = await this.fetchCovidData()
    this.dataDate = data.Date
    this.stats = data.Global
    this.countries = data.Countries
    this.title = "Global"
    this.loading = false
    }
  },

  async created() {
    const data = await this.fetchCovidData()
    this.dataDate = data.Date
    this.stats = data.Global
    this.countries = data.Countries
    this.loading = false


}
}
</script>
