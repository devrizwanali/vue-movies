<template>
  <main class="main">
    <TopNav
      :title="metaTitle" />

    <Listing
      v-if="items && items.length"
      :title="title"
      :items="items"
      :loading="loading"
      :is-tv="false"
      @loadMore="loadMore" />
  </main>
</template>

<script>
import { getTrending, getMovies, getListItem } from '~/api';
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
      page: 1
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
      return "Trending movies";
    },
  },

  async asyncData ({ params, error }) {
    try {
      const items = await getTrending('movies');
      return { items };
    } catch {
      error({ message: 'Page not found' });
    }
  },

  methods: {
    loadMore () {
      this.loading = true;
      this.page += 1;

      getTrending('movies', this.page).then((response) => {
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
