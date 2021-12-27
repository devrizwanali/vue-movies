<template>
  <main class="main">
    <TopNav
      :title="metaTitle" />

    <Hero
      :item="item" />

    <MediaNav
      :menu="menu"
      @clicked="navClicked" />

    <template v-if="activeMenu === 'overview'">
      <TvInfo
        :item="item" />

      <Credits
        v-if="showCredits"
        :people="item.cast_and_crew" />
    </template>

    <template v-if="activeMenu === 'videos' && showVideos">
      <Videos
        :videos="item.videos" />
    </template>

    <ListingCarousel
      v-if="item.related_movie && item.related_movie.length"
      title="More Like This"
      :items="item.related_movie" />
  </main>
</template>

<script>
import { apiImgUrl, getTvShow, getRelatedTvSeries } from '~/api';
import { name, yearStart, yearEnd } from '~/mixins/Details';
import TopNav from '~/components/global/TopNav';
import Hero from '~/components/Hero';
import MediaNav from '~/components/MediaNav';
import TvInfo from '~/components/tv/TvInfo';
import Videos from '~/components/Videos';
import Images from '~/components/Images';
import Credits from '~/components/Credits';
import Episodes from '~/components/tv/Episodes';
import ListingCarousel from '~/components/ListingCarousel';

export default {
  components: {
    TopNav,
    Hero,
    MediaNav,
    TvInfo,
    Videos,
    Images,
    Credits,
    Episodes,
    ListingCarousel,
  },

  mixins: [
    name,
    yearStart,
    yearEnd,
  ],

  head () {
    return {
      title: this.metaTitle,
      meta: [
        { hid: 'og:title', property: 'og:title', content: this.metaTitle },
        { hid: 'og:description', property: 'og:description', content: this.metaDescription },
        { hid: 'description', name: 'description', content: this.metaDescription },
        { hid: 'og:image', property: 'og:image', content: this.metaImage },
        { hid: 'og:url', property: 'og:url', content: `${process.env.FRONTEND_URL}${this.$route.path}` },
      ],
      bodyAttrs: {
        class: 'topnav-active',
      },
    };
  },

  data () {
    return {
      menu: [],
      activeMenu: 'overview',
    };
  },

  computed: {
    metaTitle () {
      return this.item.title
    },

    metaDescription () {
      if (this.item.description) {
        return this.truncate(this.item.description, 200);
      } else {
        return '';
      }
    },

    metaImage () {
      if (this.item.poster_path) {
        return `${apiImgUrl}/w500${this.item.poster_path}`;
      } else {
        return '';
      }
    },

    showCredits () {
      const credits = this.item.cast;
      return credits && credits.length;
    },

    showVideos () {
      const videos = this.item.videos;
      return videos && videos.length;
    },
  },

  async asyncData ({ params, error }) {
    try {
      const item = await getTvShow(params.id, 'movie');
      return { item };
    } catch {
      error({ message: 'Page not found' });
    }
  },

  created () {
    console.log(this.item)
    this.createMenu();
  },

  methods: {
    truncate (string, length) {
      return this.$options.filters.truncate(string, length);
    },

    createMenu () {
      const menu = [];
      // overview
      menu.push('Overview');
      // videos
      // if (this.showVideos) menu.push('Videos');

      this.menu = menu;
    },

    navClicked (label) {
      this.activeMenu = label;
    },
  },
};
</script>
