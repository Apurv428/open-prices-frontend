<template>
  <v-row>
    <v-col cols="12" sm="6">
      <v-card :title="username" prepend-icon="mdi-account">
        <v-card-text>
          <PriceCountChip :count="userPriceTotal" :withLabel="true"></PriceCountChip>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>

  <v-row class="mt-0">
    <v-col cols="12">
      <OpenFoodFactsButton type="editor" :value="username"></OpenFoodFactsButton>
      <ShareButton></ShareButton>
    </v-col>
  </v-row>

  <br />

  <h2 class="text-h6 mb-1">
    {{ $t('UserDetail.LatestPrices') }}
    <v-progress-circular v-if="loading" indeterminate :size="30"></v-progress-circular>
  </h2>

  <v-row>
    <v-col cols="12" sm="6" md="4" v-for="price in userPriceList" :key="price">
      <PriceCard :price="price" :product="price.product" elevation="1" height="100%"></PriceCard>
    </v-col>
  </v-row>

  <v-row v-if="userPriceList.length < userPriceTotal" class="mb-2">
    <v-col align="center">
      <v-btn size="small" :loading="loading" @click="getUserPrices">{{ $t('UserDetail.LoadMore') }}</v-btn>
    </v-col>
  </v-row>
</template>

<script>
import { mapStores } from 'pinia'
import { useAppStore } from '../store'
import api from '../services/api'
import PriceCountChip from '../components/PriceCountChip.vue'
import PriceCard from '../components/PriceCard.vue'
import OpenFoodFactsButton from '../components/OpenFoodFactsButton.vue'
import ShareButton from '../components/ShareButton.vue'

export default {
  components: {
    PriceCountChip,
    PriceCard,
    OpenFoodFactsButton,
    ShareButton,
  },
  data() {
    return {
      userPriceList: [],
      userPriceTotal: null,
      userPricePage: 0,
      loading: false,
    }
  },
  computed: {
    ...mapStores(useAppStore),
    username() {
      return this.$route.params.username
    },
  },
  mounted() {
    this.getUserPrices()
  },
  methods: {
    getUserPrices() {
      this.loading = true
      this.userPricePage += 1
      return api.getPrices({ owner: this.username, page: this.userPricePage })
        .then((data) => {
          this.userPriceList.push(...data.items)
          this.userPriceTotal = data.total
          this.loading = false
        })
    },
  }
}
</script>
