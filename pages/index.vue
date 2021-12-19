<template>
  <main class="main">
    <carousel :per-page="1" :autoplay="true"  :perPage="2" :spacePadding="40" :loop="true">
      <slide v-for="slide in featured" :key="slide.id">
        <img
          v-if="slide.image_link"
          v-lazyload="slide.image_link"
          :alt="slide.title"
          style="width: -webkit-fill-available; margin-left: 30px; margin-right: 20px"
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
import { getTrending, getMovie, getTvShow, getListItem, getHomeContent } from '~/api';
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
};
</script>
