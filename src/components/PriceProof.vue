<template>
  <v-chip
    style="padding-left:5px;padding-right:5px"
    label
    size="small"
    density="comfortable"
    :title="$t('PriceCard.Proof')"
    @click="openDialog">
    <v-icon icon="mdi-image"></v-icon>
  </v-chip>

  <v-dialog v-model="dialog" max-height="80%" width="80%">
    <v-card>
      <v-card-title>
        {{ $t('PriceCard.Proof') }} <v-btn style="float:right;" variant="text" density="compact" icon="mdi-close" @click="closeDialog"></v-btn>
      </v-card-title>
      <v-divider></v-divider>
      <v-card-text style="overflow-y:scroll;">
        <v-img :src="proofUrl"></v-img>
      </v-card-text>
      <v-divider></v-divider>
      <v-card-text>
        <ProofFooter :proof="proof"></ProofFooter>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
import ProofFooter from '../components/ProofFooter.vue'

export default {
  components: {
    ProofFooter,
  },
  props: {
    'proof': null,
    'readonly': false
  },
  data() {
    return {
      dialog: false
    }
  },
  computed: {
    proofUrl() {
      // return 'https://prices.openfoodfacts.org/img/0002/qU59gK8PQw.webp'
      // return 'https://prices.openfoodfacts.net/img/0001/lZGFga9ZOT.webp'
      return `${import.meta.env.VITE_OPEN_PRICES_APP_URL}/img/${this.proof.file_path}`
    }
  },
  methods: {
    openDialog() {
      this.dialog = true
    },
    closeDialog() {
      this.dialog = false
    }
  }
}
</script>
