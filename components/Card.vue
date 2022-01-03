<template>
  <div class="card">
    <nuxt-link
      class="card__link"
      :to="{ name: `${media}-id`, params: { id: item.videos_id } }">
      <div class="card__img">
        <img
          v-if="item.poster_url"
          v-lazyload="item.poster_url"
          class="lazyload"
          :alt="name">

        <span v-else>
          <!-- eslint-disable-next-line -->
          <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill-rule="evenodd" clip-rule="evenodd" fill="#999"><path d="M24 22h-24v-20h24v20zm-1-19h-22v18h22v-18zm-1 16h-19l4-7.492 3 3.048 5.013-7.556 6.987 12zm-11.848-2.865l-2.91-2.956-2.574 4.821h15.593l-5.303-9.108-4.806 7.243zm-4.652-11.135c1.38 0 2.5 1.12 2.5 2.5s-1.12 2.5-2.5 2.5-2.5-1.12-2.5-2.5 1.12-2.5 2.5-2.5zm0 1c.828 0 1.5.672 1.5 1.5s-.672 1.5-1.5 1.5-1.5-.672-1.5-1.5.672-1.5 1.5-1.5z"/></svg>
        </span>
      </div>

      <h2 class="card__name">
        {{ name }}
      </h2>

      <div
        v-if="(stars || item.imdb_rating)"
        class="card__rating">
        <div
          v-if="stars"
          class="card__stars">
          <div :style="{ width: `${stars}%` }" />
        </div>

        <div
          v-if="item.imdb_rating"
          class="card__vote">
          {{ item.imdb_rating | rating }}
        </div>
      </div>
    </nuxt-link>
  </div>
</template>

<script>
import { name, stars } from '~/mixins/Details';

export default {
  mixins: [
    name,
    stars,
  ],

  props: {
    item: {
      type: Object,
      required: true,
    },
    isTv: {
      type: Boolean
    }
  },
  computed: {
    media () {
      if (this.isTv || this.item.is_tvseries == "1") {
        return 'tvseries';
      } else {
        return 'movies';
      }
    },
  },
};
</script>