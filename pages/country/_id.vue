<template>
  <main class="main">
    <Listing
      v-if="items && items.length"
      :title="title"
      :items="items"
      :loading="loading"
      @loadMore="loadMore" />
  </main>
</template>

<script>
import { searchByCountry } from '~/api';
import Listing from '~/components/Listing';

export default {
  components: {
    Listing,
  },

  data () {
    return {
      loading: false,
      page: 1
    };
  },

  async asyncData ({ params, error }) {
    try {
      const items = await searchByCountry(params.id);
      return { items };
    } catch {
      error({ message: 'Page not found' });
    }
  },


  methods: {
    loadMore () {
      this.loading = true;
      this.page += 1;

      searchByCountry(this.$route.params.id, this.page).then((response) => {
        this.items = this.items.concat(response);
        this.items.page = response.page;
        this.loading = false;
      }).catch(() => {
        this.loading = false;
      });
    }
  },
};
</script>

<style lang="scss">
@import '~/assets/css/utilities/_variables.scss';

.page-search .main {
  padding-top: 6rem;

  @media (min-width: $breakpoint-large) {
    padding-top: 8rem;
  }
}
</style>
