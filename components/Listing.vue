<template>
  <div class="listing">
    <div
      v-if="title || viewAllUrl"
      class="listing__head">
      <h2
        v-if="title"
        class="listing__title">
        {{ title }}
      </h2>

      <nuxt-link
        v-if="viewAllUrl"
        :to="viewAllUrl"
        class="listing__explore">
        <strong>Explore All</strong>
      </nuxt-link>
    </div>

    <div class="listing__items">
      <Card
        v-for="item in items"
        :key="`card-${item.videos_id}`"
        :is-tv="isTv"
        :item="item" />
    </div>

    <div
      class="listing__nav">
      <div v-if="loading">
        <!-- eslint-disable-next-line -->
        <svg xmlns="http://www.w3.org/2000/svg" width="44" height="44" viewBox="0 0 44 44" stroke="#2196f3"><g fill="none" fill-rule="evenodd" stroke-width="2"><circle cx="22" cy="22" r="1"><animate attributeName="r" begin="0s" dur="1.8s" values="1; 20" calcMode="spline" keyTimes="0; 1" keySplines="0.165, 0.84, 0.44, 1" repeatCount="indefinite"/><animate attributeName="stroke-opacity" begin="0s" dur="1.8s" values="1; 0" calcMode="spline" keyTimes="0; 1" keySplines="0.3, 0.61, 0.355, 1" repeatCount="indefinite"/></circle><circle cx="22" cy="22" r="1"><animate attributeName="r" begin="-0.9s" dur="1.8s" values="1; 20" calcMode="spline" keyTimes="0; 1" keySplines="0.165, 0.84, 0.44, 1" repeatCount="indefinite"/><animate attributeName="stroke-opacity" begin="-0.9s" dur="1.8s" values="1; 0" calcMode="spline" keyTimes="0; 1" keySplines="0.3, 0.61, 0.355, 1" repeatCount="indefinite"/></circle></g></svg>
      </div>
    </div>
  </div>
</template>

<script>
import { debounce } from '~/mixins/Functions';
import Card from '~/components/Card';

export default {
  components: {
    Card,
  },

  props: {
    title: {
      type: String,
      required: false,
      default: '',
    },

    viewAllUrl: {
      type: Object,
      required: false,
      default: function () {
        return null;
      },
    },

    items: {
      type: Array,
      required: true,
    },

    show: {
      type: Number,
      required: false,
      default: null,
    },

    loading: {
      type: Boolean,
      required: false,
      default: false,
    },

    isTv: {
      type: Boolean
    }
  },

  mounted () {
    window.addEventListener('scroll', this.getScrollPosition);
  },

  beforeDestroy () {
    window.removeEventListener('scroll', this.getScrollPosition);
  },

  methods: {
    getScrollPosition: debounce(function () {
        const bottomOfWindow = (window.innerHeight + window.pageYOffset) >= document.body.offsetHeight - 600;
        if (bottomOfWindow && !this.loading) this.loadMore();
        if (this.items.length == 0) window.removeEventListener('scroll', this.getScrollPosition);
    }, 50),

    loadMore () {
      this.$emit('loadMore');
    },
  },
};
</script>
