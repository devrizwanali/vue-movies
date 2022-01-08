<template>
  <div class="spacing" :class="$style.info">
    <div :class="$style.left">
      <div :class="$style.poster">
        <img
          v-if="item.thumbnail_url"
          v-lazyload="item.thumbnail_url"
          class="lazyload"
          :alt="name">

        <span v-else>
          <!-- eslint-disable-next-line -->
          <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill-rule="evenodd" clip-rule="evenodd" fill="#999"><path d="M24 22h-24v-20h24v20zm-1-19h-22v18h22v-18zm-1 16h-19l4-7.492 3 3.048 5.013-7.556 6.987 12zm-11.848-2.865l-2.91-2.956-2.574 4.821h15.593l-5.303-9.108-4.806 7.243zm-4.652-11.135c1.38 0 2.5 1.12 2.5 2.5s-1.12 2.5-2.5 2.5-2.5-1.12-2.5-2.5 1.12-2.5 2.5-2.5zm0 1c.828 0 1.5.672 1.5 1.5s-.672 1.5-1.5 1.5-1.5-.672-1.5-1.5.672-1.5 1.5-1.5z"/></svg>
        </span>
      </div>
    </div>

    <div :class="$style.right">
      <div
        v-if="item.description"
        :class="$style.overview">
        <h2 :class="$style.title">
          Storyline
        </h2>

        <div v-html="item.description" />
      </div>

      <div :class="$style.stats">
        <ul class="nolist">
          <li v-if="item.title">
            <div :class="$style.label">
              Name
            </div>

            <div :class="$style.value">
              {{ item.title }}
            </div>
          </li>
          <li v-if="item.release">
            <div :class="$style.label">
              Release
            </div>

            <div :class="$style.value">
              {{ item.release }}
            </div>
          </li>
          <li v-if="item.runtime">
            <div :class="$style.label">
              Runtime
            </div>

            <div :class="$style.value">
              {{ item.runtime }}
            </div>
          </li>
          <li v-if="item.rating">
            <div :class="$style.label">
              Rating
            </div>

            <div :class="$style.value">
              {{ item.rating }}
            </div>
          </li>
          <li v-if="item.director && item.director.length">
            <div :class="$style.label">
              Director
            </div>

            <div
              :class="$style.value"
              v-html="formatData(item.director)" />
          </li>

          <li v-if="item.network && item.network.length">
            <div :class="$style.label">
              Network
            </div>

            <div
              :class="$style.value"
              v-html="formatData(item.network)" />
          </li>
          <li v-if="item.genre && item.genre.length">
            <div :class="$style.label">
              Genre
            </div>

            <div
              :class="$style.value"
              v-html="formatData(item.genre)" />
          </li>
          <li v-if="item.country && item.country.length">
            <div :class="$style.label">
              Country
            </div>

            <div v-if="item.country">
              <span v-for="coun in item.country">
                 <nuxt-link :to="{ name: 'country-id', params: { id: coun.country_id } }">
                  {{ coun.name }}
                </nuxt-link>
                <span v-if="coun.name"> &nbsp;</span>
              </span>
            </div>
          </li>
        </ul>
      </div>

     <!--  <div :class="$style.external">
        <ExternalLinks
          :links="item.external_ids" />
      </div> -->
    </div>
  </div>
</template>

<script>
import { apiImgUrl } from '~/api';
import { name, creators } from '~/mixins/Details';
import ExternalLinks from '~/components/ExternalLinks';

export default {
  components: {
    ExternalLinks,
  },

  mixins: [
    name,
    creators,
  ],

  props: {
    item: {
      type: Object,
      required: true,
    },
  },
  created () {
    if (this.item.homepage) {
      this.item.external_ids.homepage = this.item.homepage;
    }
  },

  methods: {
    formatData (data) {
      return data.map(x => x.name).join(', ');
    },
    formatRunTime (times) {
      return times.map(time => `${time}m`).join(', ');
    },
  },
};
</script>

<style lang="scss" module>
@import '~/assets/css/utilities/_variables.scss';

.info {
  @media (min-width: $breakpoint-medium) {
    display: flex;
  }
}

.left {
  display: none;

  @media (min-width: $breakpoint-medium) {
    display: block;
    width: 25%;
    max-width: 400px;
    padding-right: 3rem;
  }

  @media (min-width: $breakpoint-large) {
    padding-right: 5rem;
  }
}

.right {
  @media (min-width: $breakpoint-medium) {
    flex: 1;
  }
}

.poster {
  position: relative;
  height: 0;
  padding-top: 150.27%;
  overflow: hidden;
  background-color: $secondary-color;

  img,
  span {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  span {
    display: flex;
    align-items: center;
    justify-content: center;
  }
}

.overview {
  max-width: 1000px;
  margin-bottom: 3rem;
  font-size: 1.5rem;
  color: $text-color;

  @media (min-width: $breakpoint-large) {
    font-size: 1.6rem;
  }
}

.title {
  margin-bottom: 1rem;
  font-size: 1.8rem;
  color: #fff;
  letter-spacing: $letter-spacing;

  @media (min-width: $breakpoint-large) {
    font-size: 2.4rem;
  }
}

.stats {
  margin-bottom: 3rem;
  font-size: 1.5rem;
  color: $text-color;

  @media (min-width: $breakpoint-large) {
    font-size: 1.6rem;
  }

  ul {
    @media (min-width: $breakpoint-medium) {
      display: flex;
      flex-wrap: wrap;
    }
  }

  li {
    display: flex;
    padding: 0.2rem 0;

    @media (min-width: $breakpoint-medium) {
      width: 50%;
    }

    @media (min-width: $breakpoint-xlarge) {
      width: 100%;
    }
  }

  a {
    color: $primary-color;
    text-decoration: underline;
  }
}

.label {
  flex: 1;
  max-width: 90px;
  margin-right: 1.5rem;
  color: #fff;

  @media (min-width: $breakpoint-xsmall) {
    max-width: 110px;
  }
}

.value {
  flex: 2;
}

.external {
  ul {
    display: flex;
    margin-left: -0.5rem;
  }

  a {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 4.4rem;
    height: 4.4rem;

    svg {
      transition: all 0.3s ease-in-out;
    }

    &:hover,
    &:focus {
      svg {
        fill: $primary-color;
      }
    }
  }
}
</style>
