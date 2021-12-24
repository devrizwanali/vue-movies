<template>
  <main class="main">
    <carousel :autoplay="false"  :perPageCustom="[[768, 2], [100, 1]]" :spacePadding="40" :loop="false">
      <slide v-for="slide in featured" :key="slide.id">
        <img
          v-if="slide.image_link"
          :src="slide.image_link"
          :alt="slide.title"
          @click="handleClick(slide)"
          style="width: -webkit-fill-available;
                 margin-left: 30px;
                 margin-right: 20px;
                 cursor: pointer;"
        >
      </slide>
      </carousel>

    <ListingCarousel
      v-if="trendingMovies && trendingMovies.length"
      :title="trendingMoviesTitle"
      :view-all-url="trendingMoviesUrl"
      :items="trendingMovies" />

    <ListingCarousel
      v-if="trendingTv && trendingTv.length"
      :title="trendingTvTitle"
      :view-all-url="trendingTvUrl"
      :items="trendingTv" />
  </main>
</template>

<script>
import { getHomeContent } from '~/api';
import ListingCarousel from '~/components/ListingCarousel';

export default {
  components: {
    ListingCarousel,
  },

  computed: {
    trendingMoviesTitle () {
      return 'Trending Movies'
    },

    trendingMoviesUrl () {
      return { name: 'movie-category-name', params: { name: 'trending' } };
    },

    trendingTvTitle () {
      return 'Trending TV Shows'
    },

    trendingTvUrl () {
      return { name: 'tv-category-name', params: { name: 'trending' } };
    },
  },

  async asyncData ({ error }) {
    try {

      const data = await getHomeContent();
      const trendingMovies = data.latest_movies;

      const trendingTv = data.latest_tvseries;
      const featured = data.slider.slide

      return { trendingMovies, trendingTv, featured };
    } catch {
      error({ statusCode: 504, message: 'Data not available' });
    }
  },
  methods: {
    handleClick(slide) {
      if(slide.action_type == 'tvseries') {
        this.$router.push(`/tv/${slide.id}`)
      } else {
        this.$router.push(`/movie/${slide.id}`)
      }
    }
  }
};
</script>

<style>
@media only screen and (max-width: 600px) {
  slide > img: {
    width: 100%;
    background: red;
  }

}

</style>