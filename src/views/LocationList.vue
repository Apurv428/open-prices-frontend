<template>
  <h1 class="text-h5 mb-1">
    {{ $t('LocationList.Title') }}
    <v-progress-circular v-if="loading" indeterminate :size="30"></v-progress-circular>
  </h1>

  <v-row v-if="!loading">
    <v-col>
      <v-chip class="mr-2" label variant="text" prepend-icon="mdi-map-marker-outline">
        {{ $t('LocationList.LocationTotal', { count: locationTotal }) }}
      </v-chip>
    </v-col>
  </v-row>

  <v-row class="mt-0">
    <v-col cols="12" sm="6" md="4" v-for="location in locationList" :key="location">
      <v-card
        :title="getLocationTitle(location)"
        :subtitle="location.osm_display_name"
        prepend-icon="mdi-map-marker-outline"
        elevation="1"
        height="100%"
        @click="goToLocation(location)">
        <v-card-text>
          <PriceCountChip :count="location.price_count" :withLabel="true"></PriceCountChip>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>

  <v-row v-if="locationList.length < locationTotal" class="mb-2">
    <v-col align="center">
      <v-btn size="small" :loading="loading" @click="getLocations">{{ $t('LocationList.LoadMore') }}</v-btn>
    </v-col>
  </v-row>
</template>

<script>
import api from '../services/api'
import utils from '../utils.js'
import PriceCountChip from '../components/PriceCountChip.vue'

export default {
  components: {
    PriceCountChip,
  },
  data() {
    return {
      // data
      locationList: [],
      locationTotal: null,
      locationPage: 0,
      loading: false,
    }
  },
  computed: {},
  mounted() {
    this.initLocationList()
  },
  methods: {
    initLocationList() {
      this.locationList = []
      this.locationPage = 0
      this.getLocations()
    },
    getLocations() {
      this.loading = true
      this.locationPage += 1
      return api.getLocations({ order_by: '-price_count', page: this.locationPage })
        .then((data) => {
          this.locationList.push(...data.items)
          this.locationTotal = data.total
          this.loading = false
        })
    },
    getLocationTitle(location) {
      return utils.getLocationTitle(location, true, false, true, true)
    },
    goToLocation(location) {
      this.$router.push({ path: `/locations/${location.id}` })
    },
  }
}
</script>
