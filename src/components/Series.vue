<template>
  <div class="hello">
    <h3>WATCH IPL LIVE</h3>
    <h3 @click="tamilIpl=!tamilIpl">TAMIL</h3>
    <div v-show="tamilIpl">
      <video id="my_video_1" class="video-js vjs-fluid vjs-default-skin" controls preload="auto"
      data-setup='{}' autoplay="autoplay">
        <source src="https://vmi859463.contaboserver.net/Starsports_tamil/index.m3u8?lang=eng" type="application/x-mpegURL">
      </video>
    </div>
    <h3 @click="englishIpl=!englishIpl">ENGLISH</h3>
    <iframe v-show="englishIpl" allow="autoplay 'none'" autoplay="0" autostart="false" width="600" height="500" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen  src="http://c2.hitcric.live/" scrolling="no"></iframe>
    <br><br>
    <div>
      <input type="text" v-model="searchTerm" /><br>
      <button @click="getImdbId" @keyup.enter="getImdbId">SEARCH</button>
    </div>
    <br><br>
     <iframe
      v-if="selectedEpisode.season" 
      :src="`https://www.2embed.ru/embed/imdb/tv?id=${searchData.imdbId}&s=${selectedEpisode.season}&e=${selectedEpisode.episode}`"
      width="900"
      height="500"/>
      <br><br>
    <ul v-for="(season, seasonIndex) in searchData.seasons" :key="seasonIndex">
      Season {{ season.number }}
      <li v-for="(episodes, index) in season.episodes.reverse()" :key="index">
        <button @click="playEpisode(season.number, index+1)">E{{ index+1 }}</button>
      </li>
    </ul>
    <!-- <div v-for="(serie, index) in seriesData" :key="index">
      <h3>{{`${serie.name} Latest Episode`}}</h3>
      <iframe
      v-if="serie.season" 
      :src="`https://www.2embed.ru/embed/imdb/tv?id=${serie.imdbId}&s=${serie.season}&e=${serie.episode}`"
      width="900"
      height="500"/>
    </div> -->
  </div>
</template>

<script>
export default {
  name: '',
  data: () => {
    return {
      apiKey: 'ed97152c',
      englishIpl: false,
      tamilIpl: false,
      seriesConfig: [
        {
          imdbId: 'tt2741602',
          name: 'The Blacklist'
        },
        {
          imdbId: 'tt7235466',
          name: '9-1-1'
        },
        {
          imdbId: 'tt7587890',
          name: 'The Rookie'
        }
      ],
      seriesData: [],
      searchTerm: '',
      searchData: {},
      selectedEpisode: {}
    }
  },
  computed: {
    series () {
      return this.seriesConfig;
    }
  },
  async mounted () {
    // this.seriesConfig.forEach(async (serie) => {
    //   const seriesData = await this.getReq(`https://api.tvmaze.com/singlesearch/shows?q=${serie.name.replace(' ', '%20')}&embed=episodes`);
    //   const episodes = seriesData._embedded.episodes.filter(i => this.dateDiffInDays(new Date(i.airstamp), new Date()) >= 0);
    //   const latestEpisode = episodes[episodes.length - 1];
    //   const latestEpisodeInfo = {
    //     ...serie,
    //     episode: latestEpisode.number,
    //     season: latestEpisode.season
    //   }
    //   this.seriesData.push(latestEpisodeInfo)
    // });
  },
  methods: {
    async getReq (url) {
      return fetch(url, {proxy: 'localhost:8080'}).then(res => res.json());
    },
    async getImdbId () {
      const url = `http://www.omdbapi.com/?t=${this.searchTerm.replace(' ', '+')}&apikey=${this.apiKey}`;
      let response = await this.getReq(url);
      const seriesData = await this.getReq(`https://api.tvmaze.com/singlesearch/shows?q=${this.searchTerm.replace(' ', '%20')}&embed=episodes`);
      if (seriesData) {
        this.searchData = {
          image: seriesData.image.medium,
          summary: seriesData.summary,
          schedule: seriesData.schedule,
          imdbId: response.imdbID,
          seasons: {}
        };
        let episodes = seriesData._embedded.episodes;
        episodes.forEach((e) => {
          if (this.searchData.seasons[`s${e.season}`]){
            if (this.searchData.seasons[`s${e.season}`].episodes) {
              this.searchData.seasons[`s${e.season}`].episodes.push(e);
            } else {
              this.searchData.seasons[`s${e.season}`].episodes = [];
              this.searchData.seasons[`s${e.season}`].episodes.push(e);
            }
          } else {
            this.searchData.seasons[`s${e.season}`] = {
              number: e.season
            };
            if (this.searchData.seasons[`s${e.season}`].episodes) {
              this.searchData.seasons[`s${e.season}`].episodes.push(e);
            } else {
              this.searchData.seasons[`s${e.season}`].episodes = [];
              this.searchData.seasons[`s${e.season}`].episodes.push(e);
            }
          }
        });
      }
    },
    // a and b are javascript Date objects
    dateDiffInDays(a, b) {
      // Discard the time and time-zone information.
      const _MS_PER_DAY = 1000 * 60 * 60 * 24;
      const utc1 = Date.UTC(a.getFullYear(), a.getMonth(), a.getDate());
      const utc2 = Date.UTC(b.getFullYear(), b.getMonth(), b.getDate());

      return Math.floor((utc2 - utc1) / _MS_PER_DAY);
    },
    playEpisode (season,episode) {
      this.selectedEpisode = {
        season,
        episode
      }
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
