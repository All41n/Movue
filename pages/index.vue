<template>
  <v-app id="inspire">
    <Showcase :items="gettingRandom" />
    <v-subheader id="page_heading">Trending This Week</v-subheader>
    <Slidegroups
      :items="movies"
      :url="trendingMoviesURL('movie')"
      :title="sliderTitle('Trending Movies')"
    />
    <Slidegroups
      :items="tv"
      :url="trendingTVURL('tv')"
      :title="sliderTitle('Trending Tv Series')"
    />
  </v-app>
</template>

<script>
import Slidegroups from '../components/Slidergroups'
import Showcase from '../components/Showcase'
import { fetchCollections, fetchTrending } from '../tmdb/tmdb'
/**
 * https://nuxtjs.org/guide/async-data/
 */
export default {
  components: {
    Showcase,
    Slidegroups,
  },
  methods: {
    trendingMoviesURL: function (mediatype) {
      return { name: 'discover-media-trending', params: { media: mediatype } }
    },
    trendingTVURL: function (mediatype) {
      return { name: 'discover-media-trending', params: { media: mediatype } }
    },
    sliderTitle(title) {
      return title
    },
  },
  async asyncData({ error }) {
    try {
      const popularMovie = await fetchCollections('movie', 'popular')
      const popularSeries = await fetchCollections('tv', 'popular')
      popularMovie.results.forEach(function (e) {
        e.media_type = "movie"
      })
      popularSeries.results.forEach(function (e) {
        e.media_type = "tv"
      })
      //return movies at number n
      const popular = [...popularMovie.results,...popularSeries.results]
      const returnLimit = []
      const getRandom = (arr, num = 1) => {
        for (let i = 0; i < num; ) {
          const random = Math.floor(Math.random() * arr.length)
          if (returnLimit.indexOf(arr[random]) !== -1) {
            continue
          }
          returnLimit.push(arr[random])
          i++
        }
        return returnLimit
      }

      const gettingRandom = getRandom(popular, 5)
      const movies = await fetchTrending('movie', 'week')
      const tv = await fetchTrending('tv', 'week')
      // const movies = [...trendingMovies.results]
      // const tv = [...trendingTv.results]

      // gettingRandom.forEach(function(e){
      //   e.media_type = "'movie"
      // })

      return { gettingRandom, movies, tv }
    } catch {
      error({ message: 'Data cannot be accessed!' })
    }
  },
}
</script>

<style scoped>
#inspire {
  overflow-y: auto;
  background-color: rgb(230, 230, 230);
}

#page_heading {
  font-size: 30px;
  color: #4527a0;
  margin-bottom: 10px;
  /* background-color: rgb(230, 230, 230); */
}
</style>