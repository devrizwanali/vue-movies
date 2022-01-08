<template>
  <main class="main">
    <TopNav
      :title="metaTitle" />

    <Listing
      v-if="items && items.length"
      :title="title"
      :is-tv="true"
      :items="items"
      :loading="loading"
      @loadMore="loadMore" />
  </main>
</template>

<script>
import { getTrending, getTvShows, getListItem } from '~/api';
import TopNav from '~/components/global/TopNav';
import Listing from '~/components/Listing';

export default {
  components: {
    TopNav,
    Listing,
  },

  data () {
    return {
      loading: false,
      page: 1,
    };
  },

  head () {
    return {
      title: this.metaTitle,
      meta: [
        { hid: 'og:title', property: 'og:title', content: this.metaTitle },
        { hid: 'og:url', property: 'og:url', content: `${process.env.FRONTEND_URL}${this.$route.path}` },
      ],
      bodyAttrs: {
        class: 'topnav-active',
      },
    };
  },

  computed: {
    metaTitle () {
      return this.title;
    },

    title () {
      return "Trending TV Shows";
    },
  },

  async asyncData ({ params, error }) {
    try {
      const items = await getTrending('tvseries');
      return { items };
    } catch {
      error({ message: 'Page not found' });
    }
  },

  methods: {
    loadMore () {
      this.loading = true;
      this.page += 1;

      getTrending('tvseries', this.page).then((response) => {
        this.items = this.items.concat(response);
        this.items.page = response.page;
        this.loading = false;
      }).catch(() => {
        this.loading = false;
      });
    },
  },
};
</script>
