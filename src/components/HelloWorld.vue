<template>
  <div class="hello">
    <div>Search</div>
    <input type="text" v-model="searchTerm"/><br/>
    <button @click="search">Search</button><br/>
    <!-- <iframe v-if="searchRes" :src="`https://www.2embed.ru/embed/imdb/tv?id=${searchRes.imdbID}&s=9&e=10`"/> -->
    <template>
      <ul v-for="(season, seasonIndex) in series.seasons" :key="seasonIndex">
        <li v-for="(episodes, index) in season.episodes.reverse()" :key="index">
          S{{ episodes.season }}E{{ index+1 }} - {{ new Date(episodes.airstamp) }}
        </li>
      </ul>
    </template>
  </div>
</template>

<script>
export default {
  name: '',
  data: () => {
    return {
      series: {},
      searchTerm: '',
      searchRes: null,
      apiKey: 'ed97152c'
    }
  },
  async mounted () {
//     let response = await this.getReq(`'https://api.tvmaze.com/singlesearch/shows?q=the%20blacklist&embed=episodes'`);
//     if (response) {
// this.series = {
//       image: response.image.medium,
//       summary: response.summary,
//       schedule: response.schedule,
//       seasons: {}
//     };
//     let episodes = response._embedded.episodes;
//     episodes.forEach((e) => {
//       if (this.series.seasons[`s${e.season}`]){
//         if (this.series.seasons[`s${e.season}`].episodes) {
//           this.series.seasons[`s${e.season}`].episodes.push(e);
//         } else {
//           this.series.seasons[`s${e.season}`].episodes = [];
//           this.series.seasons[`s${e.season}`].episodes.push(e);
//         }
//       } else {
//         this.series.seasons[`s${e.season}`] = {};
//         if (this.series.seasons[`s${e.season}`].episodes) {
//           this.series.seasons[`s${e.season}`].episodes.push(e);
//         } else {
//           this.series.seasons[`s${e.season}`].episodes = [];
//           this.series.seasons[`s${e.season}`].episodes.push(e);
//         }
//       }
//     })
//     }
    
  },
  methods: {
    async getReq (url) {
      return fetch(url, {proxy: 'localhost:8080'}).then(res => res.json());
    },
    async search () {
      const url = `http://www.omdbapi.com/?t=${this.searchTerm.replace(' ', '+')}&apikey=${this.apiKey}`;
      let response = await this.getReq(url);
      console.log(response.imdbID)
      this.searchRes = response;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
