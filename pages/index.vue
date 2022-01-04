<template>
  <main class="main">
    <swiper ref="mySwiper" 
      class="swiper"
      :auto-update="true"
      :options="swiperOptions">
        <swiper-slide v-for="slide in featured" :key="slide.id">
        <img
          v-if="slide.image_link"
          :src="slide.image_link"
          :alt="slide.title"
          @click="handleClick(slide)"
        >
      </swiper-slide>
    </swiper>
    <div class="swiper-pagination-wrapper">
     <div class="swiper-pagination pagination-custom" slot="pagination"></div>
    </div>
    <ListingCarousel
      v-if="trendingMovies && trendingMovies.length"
      :title="trendingMoviesTitle"
      :view-all-url="trendingMoviesUrl"
      :items="trendingMovies" />

    <ListingCarousel
      v-if="trendingTv && trendingTv.length"
      :title="trendingTvTitle"
      :view-all-url="trendingTvUrl"
      :is-tv="true"
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
      return { name: 'movies' };
    },

    trendingTvTitle () {
      return 'Trending TV Shows'
    },

    trendingTvUrl () {
      return { name: 'tvseries' };
    },
      swiper() {
        return this.$refs.mySwiper.$swiper
      }
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

  data() {
      return {
        swiperOptions: {
         slidesPerView: 2,
          spaceBetween: 40,
          loop: true,
          autoplay: {
            delay: 2500,
            disableOnInteraction: false
          },
          pagination: {
            el: '.swiper-pagination',
            clickable: true
          },
          breakpoints: {
            768: {
              slidesPerView: 2,
            },
            320: {
              slidesPerView: 1,
            }
          }
        }
      }
    },

     mounted() {
      console.log('Current Swiper instance object', this.swiper)
      this.swiper.slideTo(3, 1000, false)
    },
  methods: {
    handleClick(slide) {
      if(slide.action_type == 'tvseries') {
        this.$router.push(`/tvseries/${slide.action_id}`)
      } else {
        this.$router.push(`/movies/${slide.action_id}`)
      }
    }
  }
};
</script>

<style>
.swiper-pagination-wrapper {
  display: flex;
  justify-content: center;
  margin-top: 30px;
}

.swiper-pagination-bullet {
  width: 10px;
  height: 10px;
  display: inline-block;
  background-color: white;
  color: whtie;
  opacity: 1;
  margin-left: 10px;
  border-radius: 100%;
}

.swiper-pagination-bullet-active {
  background-color: black;
}
</style>


