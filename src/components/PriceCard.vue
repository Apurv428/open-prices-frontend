<template>
  <v-card :id="'price_' + price.id">
    <v-container class="pa-2">
      <v-row>
        <v-col v-if="!hideProductImage" style="max-width:25%">
          <v-img v-if="product && product.image_url" :src="product.image_url" style="max-height:100px;width:100px" @click="goToProduct()"></v-img>
          <v-img v-if="!product || !product.image_url" :src="productImageDefault" style="height:100px;width:100px;filter:invert(.9);"></v-img>
        </v-col>
        <v-col style="max-width:75%">
          <h3 v-if="!hideProductTitle" @click="goToProduct()">{{ getPriceProductTitle() }}</h3>

          <p v-if="!hideProductDetails" class="mb-2">
            <span v-if="hasProductBrands">
              <v-chip v-for="brand in getProductBrandsList" label size="small" density="comfortable" class="mr-1" @click="goToBrand(brand)">
                {{ brand }}
              </v-chip>
            </span>
            <span v-if="hasProductQuantity">
              <ProductQuantityChip class="mr-1" :productQuantity="product.product_quantity" :productQuantityUnit="product.product_quantity_unit"></ProductQuantityChip>
            </span>
            <span v-if="hasPriceOrigin && priceOrigin">
              <v-chip label size="small" density="comfortable" class="mr-1">
                {{ priceOrigin.name }}
              </v-chip>
            </span>
            <span v-if="hasPriceLabels">
              <v-chip v-for="pl in priceLabels" label size="small" density="comfortable" class="mr-1">
                {{ pl.name }}
                <v-icon v-if="pl.icon" end :icon="pl.icon"></v-icon>
              </v-chip>
            </span>
          </p>

          <PricePrice v-if="price" :price="price" :productQuantity="product ? product.product_quantity : null" :productQuantityUnit="product ? product.product_quantity_unit : null" :hidePriceDate="hidePriceDate"></PricePrice>
        </v-col>
      </v-row>

      <PriceFooter v-if="price && !hidePriceFooter" class="mt-2" :price="price" :hidePriceLocation="hidePriceLocation" :readonly="readonly"></PriceFooter>
    </v-container>
  </v-card>
</template>

<script>
import utils from '../utils.js'
import OriginTags from '../data/origins-tags.json'
import LabelsTags from '../data/labels-tags.json'
import ProductQuantityChip from '../components/ProductQuantityChip.vue'
import PricePrice from '../components/PricePrice.vue'
import PriceFooter from '../components/PriceFooter.vue'

export default {
  components: {
    ProductQuantityChip,
    PricePrice,
    PriceFooter
  },
  props: {
    'price': null,
    'product': null,
    'hideProductImage': false,
    'hideProductTitle': false,
    'hideProductDetails': false,
    'hidePriceDate': false,
    'hidePriceFooter': false,
    'hidePriceLocation': false,
    'readonly': false
  },
  data() {
    return {
      productImageDefault: 'https://world.openfoodfacts.org/images/icons/dist/packaging.svg',
      priceOrigin: null,
      priceLabels: [],
    }
  },
  mounted() {
    this.initPriceCard()
  },
  computed: {
    categoryTag() {
      return this.price.category_tag
    },
    hasProduct() {
      return !!this.product
    },
    hasPrice() {
      return !!this.price
    },
    hasCategoryTag() {
      return !!this.categoryTag
    },
    hasProductQuantity() {
      return this.hasProduct && !!this.product.product_quantity
    },
    hasProductBrands() {
      return this.hasProduct && !!this.product.brands
    },
    hasPriceOrigin() {
      return this.hasPrice && !!this.price.origins_tags && this.price.origins_tags.length
    },
    hasPriceLabels() {
      return this.hasPrice && !!this.price.labels_tags && this.price.labels_tags.length
    },
    getPriceCategoryName() {
      if (this.price && this.hasCategoryTag) {
        return utils.getCategoryName(this.price.category_tag)
      }
    },
    getProductBrandsList() {
      if (this.hasProductBrands) {
        return this.product.brands.split(',')
      }
    },
  },
  methods: {
    initPriceCard() {
      this.priceOrigin = this.getPriceOriginTag()
      this.priceLabels = this.getPriceLabelsTagsList()
    },
    getPriceProductTitle() {
      if (this.hasProduct && this.product.product_name) {
        return this.product.product_name
      } else if (this.hasPrice && this.price.product_code) {
        return this.price.product_code
      } else if (this.hasPrice && this.hasCategoryTag) {
        return this.getPriceCategoryName
      }
      return this.$t('PriceCard.UnknownProduct')
    },
    getPriceProductCode() {
      if (this.hasProduct) {
        return this.product.code
      } else if (this.price.product_code) {
        return this.price.product_code
      } else if (this.price.category_tag) {
        return this.price.category_tag
      }
      return 'product code error'
    },
    getPriceOriginTag() {
      if (this.price && this.price.origins_tags) {
        return OriginTags.find(ot => this.price.origins_tags[0].indexOf(ot.id) > -1)
      }
    },
    getPriceLabelsTagsList() {
      if (this.price && this.price.labels_tags) {
        return LabelsTags.filter(lt => this.price.labels_tags.indexOf(lt.id) > -1)
      }
    },
    goToProduct() {
      if (this.readonly) {
        return
      }
      this.$router.push({ path: `/products/${this.getPriceProductCode()}` })
    },
    goToBrand(brand) {
      if (this.readonly) {
        return
      }
      this.$router.push({ path: `/brands/${brand}` })
    },
  },
}
</script>
