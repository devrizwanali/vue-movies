<template>
  <div class="spacing">
    <div :class="$style.head">
      <select
        v-if="seasons.length > 1"
        v-model="activeSeason"
        @change="getEpisodes">
        <option
          v-for="season in seasons"
          :key="`season-${season.season}`"
          :value="season.season">
          Season {{ season.season }}
        </option>
      </select>

      <strong
        v-if="activeEpisodes"
        :class="$style.count">
        {{ episodeCount }}
      </strong>
    </div>

    <div
      v-if="activeEpisodes"
      :class="$style.items">
      <EpisodesItem
        v-for="(episode, index) in activeEpisodes"
        :key="`episode-${episode.episodes_id}`"
        :episodeNumber="index + 1"
        :episode="episode" />
    </div>
  </div>
</template>

<script>
import { getTvShowEpisodes } from '~/api';
import EpisodesItem from '~/components/tv/EpisodesItem';

export default {
  components: {
    EpisodesItem,
  },

  props: {
    numberOfSeasons: {
      type: Number,
      required: true,
    },
    item: {
      type: Object,
      required: true
    }

  },

  data () {
    return {
      activeSeason: null,
      activeEpisodes: null,
    };
  },

  computed: {
    episodeCount () {
      return `${this.activeEpisodes.length} ${this.activeEpisodes.length > 1 ? 'Episodes' : 'Episode'}`;
    },

    seasons () {
      const seasons = [];

      for (let index = 0; index < this.item?.season?.length; index++) {
        seasons.push({
          season: this.item.season[index].seasons_name,
          episodes: this.item.season[index].episodes.length,
        });
      }

      this.activeSeason = seasons[0]?.season;
      seasons.sort((a, b) => a.season > b.season ? -1 : 1);

      return seasons;
    },
  },

  mounted () {
    this.getEpisodes();
  },

  methods: {
    getEpisodes () {
      const season = this.item?.season?.find(season => season.seasons_name === this.activeSeason);

      if (season?.episodes)
        this.activeEpisodes = season.episodes;
    },
  },
};
</script>

<style lang="scss" module>
@import '~/assets/css/utilities/_variables.scss';

.head {
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;

  @media (min-width: $breakpoint-large) {
    margin-bottom: 2rem;
  }

  select {
    margin-right: 1rem;
  }
}

.count {
  font-size: 1.2rem;
  color: $text-color-grey;
  letter-spacing: $letter-spacing;

  @media (min-width: $breakpoint-large) {
    font-size: 1.4rem;
  }
}

.items {
  display: flex;
  flex-wrap: wrap;
  margin-right: -0.4rem;
  margin-left: -0.4rem;
}
</style>
