<template>
  <div class="relative">

    <div v-if="isSearched" class="relative flex p-2 m-4 duration-500 ease-in-out rounded-md cursor-pointer placeholder-content h-detailedCard bg-m-blue-900 hover:shadow-md md:transform md:transition hover:scale-105"></div>

    <div v-else>
      <span v-if="$auth.loggedIn && watchlist" class="absolute bottom-0 right-0 z-30 w-1/3 duration-500 rounded-md">
        <div class="absolute flex justify-end w-full h-7 right-5 bottom-2">
          <div
            v-if="!watched"
            @click="update(true)"
            class="flex items-center justify-center w-12 h-full p-2 bg-gray-500 rounded-lg cursor-pointer md:w-16 focus:outline-none focus:shadow-outline-none hover:bg-gray-600"
          >
            <svg-icon name="eye" class="w-5 h-5 text-m-blue-900" />
          </div>
          <div
            v-else
            @click="update(false)"
            class="flex items-center justify-center w-12 h-full p-2 rounded-lg cursor-pointer md:w-16 bg-m-burgundy-600 focus:outline-none focus:shadow-outline-none hover:bg-m-burgundy-700"
          >
            <svg-icon name="bookmark" class="w-5 h-5 text-m-blue-900" />
          </div>
          <div
            @click="deleteMovie()"
            class="flex items-center justify-center w-12 h-full p-2 ml-2 mr-2 bg-red-400 rounded-lg cursor-pointer md:ml-3 md:w-16 focus:outline-none focus:shadow-outline-none hover:bg-red-500"
          >
            <svg class="w-5 h-5 text-m-blue-900 group-hover:text-white group-focus:text-white" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
            </svg>
          </div>
        </div>
      </span>
      <nuxt-link :to="'/movies/'+ movieInfos.id" tag="a" no-prefetch
                 :class="animation ? 'duration-500 ease-in-out hover:shadow-md md:transform md:transition hover:scale-105' : ' '"
                 class="relative flex p-2 m-4 rounded-md cursor-pointer bg-m-blue-900"
      >
        <div
          class="absolute top-0 right-0 z-0 w-full h-full bg-cover rounded-md"
          :style="movieInfos.backdrop_path !== null ? 'background:url('+ backdropPath + movieInfos.backdrop_path + ') top center / cover no-repeat,linear-gradient(black,black);opacity:0.08;' : ''"
        ></div>
        <VueLoadImage class="relative flex-shrink-0 rounded-md w-detailedCard h-detailedCard">
          <img slot="image"
               class="relative object-cover rounded-md w-detailedCard h-detailedCard"
               :src="movieInfos.poster_path !== null ? imgPath + movieInfos.poster_path : require('~/assets/img/noPoster.jpg')"
               :alt="'Poster of ' + movieInfos.title "
               :title="movieInfos.title"
          />
          <div slot="preloader" class="rounded-md w-detailedCard h-detailedCard placeholder-content"></div>
        </VueLoadImage>
        <div class="relative w-full h-full mx-2">
          <!-- Display xl-->
          <div class="hidden xl:block">
            <p
              class="font-bold text-gray-300 truncate md:text-lg sm:w-full"
            >{{ movieInfos.title.length > 70 ? movieInfos.title.slice(0,70) + '...' : movieInfos.title }}</p>

            <p
              class="w-full h-20 mt-1 text-xs text-justify text-gray-300"
            >{{ movieInfos.overview.length > 450 ? movieInfos.overview.slice(0,450) + '...' : movieInfos.overview }}</p>
          </div>
          <!-- Display sm-->
          <div class="hidden sm:block xl:hidden">
            <p
              class="font-bold text-gray-300 truncate md:text-lg sm:w-full"
            >{{ movieInfos.title.length > 50 ? movieInfos.title.slice(0,50) + '...' : movieInfos.title }}</p>

            <p
              class="w-full h-20 mt-1 text-xs text-justify text-gray-300"
            >{{ movieInfos.overview.length > 280 ? movieInfos.overview.slice(0,280) + '...' : movieInfos.overview }}</p>
          </div>
          <!-- Display std-->
          <div class="block sm:hidden xl:hidden">
            <p
              class="font-bold text-gray-300 truncate md:text-lg sm:w-full"
            >{{ movieInfos.title.length > 20 ? movieInfos.title.slice(0,20) + '...' : movieInfos.title }}</p>

            <p
              class="w-full h-20 mt-1 text-xs text-justify text-gray-300"
            >{{ movieInfos.overview.length > 110 ? movieInfos.overview.slice(0,110) + '...' : movieInfos.overview }}</p>
          </div>

          <div>
            <span
              :class="movieInfos.vote_average >= 6.0 ? 'bg-green-500' : movieInfos.vote_average >= 4.0 ? 'bg-orange-500' : movieInfos.vote_count === 0 ? 'bg-gray-500' : 'bg-red-500'"
              class="inline-flex items-center justify-center w-6 h-6 -mr-1 text-xs rounded-full right-1 bottom-1"
            >
              <span class="font-bold text-m-blue-900">{{ movieInfos.vote_count === 0 ? 'N/A' : movieInfos.vote_average }}</span>
            </span>
            <span v-if="watchlist">
              <span
                v-for="(genre, index) in movieInfos.genres.slice(0,2)"
                :key="index"
                class="items-center hidden px-3 py-1 ml-2 text-xs font-medium leading-4 text-gray-300 bg-teal-900 rounded-md sm:inline-flex"
              >{{ genre.name }}</span>
              <span
                v-if="movieInfos.genres.length > 2"
                class="items-center hidden px-3 py-1 text-xs font-medium leading-4 text-gray-300 bg-teal-900 rounded-md sm:inline-flex"
              >+{{ ' ' + movieInfos.genres.length - 2 }}</span>
            </span>
            <span v-else>
              <span
                v-for="(id, index) in movieInfos.genre_ids.slice(0,2)"
                :key="index"
                class="items-center hidden px-3 py-1 ml-2 text-xs font-medium leading-4 text-gray-300 bg-teal-900 rounded-md sm:inline-flex"
              >{{ genresList.find(genre => genre.id === id).name }}</span>
              <span
                v-if="movieInfos.genre_ids.length > 2"
                class="items-center hidden px-3 py-1 text-xs font-medium leading-4 text-gray-300 bg-teal-900 rounded-md sm:inline-flex"
              >+{{ ' ' + movieInfos.genre_ids.length - 2 }}</span>
            </span>

            <span
              class="w-full mt-2 ml-2 text-xs italic text-gray-300 sm:ml-0"
            >{{ formateDate(movieInfos.release_date) }}</span>

            <span v-if="$auth.loggedIn && !watchlist" class="absolute bottom-0 right-0">
              <div
                v-if="watched"
                class="p-1.5 bg-gray-500 rounded-full cursor-pointer focus:outline-none focus:shadow-outline-none"
              >
                <svg-icon name="eye" class="w-3 h-3 text-m-blue-900" />
              </div>
              <div
                v-if="notWatched"
                class="p-1.5 rounded-full cursor-pointer bg-m-burgundy-600 focus:outline-none focus:shadow-outline-none"
              >
                <svg-icon name="bookmark" class="w-3 h-3 text-m-blue-900" />
              </div>
            </span>

          </div>
        </div>
      </nuxt-link>
    </div>

  </div>
</template>
<script>
import { mapState, mapGetters } from 'vuex'
import VueLoadImage from '@/components/global/VueLoadImage'
export default {
  components: {
    VueLoadImage
  },
  props: {
    movieInfos: {
      type: Object,
      required: true
    },
    animation: {
      type: Boolean,
      required: false,
      default: true
    },
    isSearched: {
      type: Boolean,
      required: true
    },
    watchlist: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  data () {
    return {
      imgPath: 'https://image.tmdb.org/t/p/w92',
      backdropPath: 'https://image.tmdb.org/t/p/w300'
    }
  },
  computed: {
    ...mapState({
      genresList: state => state.tmdb.genresList,
      ...mapGetters('watchlist', ['watchedMovieList', 'notWatchedMovieList']),
      watched () {
        return this.watchedMovieList.some(movie => movie.movie_id === this.movieInfos.id)
      },
      notWatched () {
        return this.notWatchedMovieList.some(movie => movie.movie_id === this.movieInfos.id)
      }
    })
  },
  methods: {
    formateDate (date) {
      const formattedDate = new Date(date)
      return formattedDate instanceof Date && !isNaN(formattedDate) ? formattedDate.toLocaleDateString('fr-FR') : 'No date given'
    },
    async add () {
      await this.$axios.post('/watchlist/addMovie', { movieId: this.movieInfos.id })
      await this.$store.dispatch('watchlist/setWatchList')
    },
    async update (state) {
      await this.$axios.put('/watchlist/updateMovie', { movieId: this.movieInfos.id, watched: state })
      await this.$store.dispatch('watchlist/setWatchList')
    },
    async deleteMovie () {
      await this.$axios.delete('/watchlist/deleteMovie', { data: { movieId: this.movieInfos.id } })
      await this.$store.dispatch('watchlist/setWatchList')
    }
  }
}
</script>
