<template>
  <section>
    <div class="container">
      <div class="row">
        <div class="col-sm-3">
          <ShopSidebar
            page-type="shop"
            :categories-tree="categoriesTree"
          ></ShopSidebar>
        </div>

        <div
          v-if="this.products.data && this.products.data.length"
          class="col-sm-9 padding-right"
        >
          <div class="features_items">
            <h2 class="title text-center">Latest Items</h2>

            <div
              v-for="(item, index) in this.products.data"
              :key="index"
              class="col-sm-4"
            >
              <ProductTemplateNormal :item="item"></ProductTemplateNormal>
            </div>

            <FrontPagination
              :data="this.products"
              @handlePagination="paginate"
            ></FrontPagination>
          </div>
        </div>
        <div v-else class="col-sm-9 padding-right">
          <p class="text-center no-products">
            <i class="fa fa-exclamation-triangle"></i> No products found that
            match your search criteria!
          </p>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import ShopSidebar from '../components/shop-components/ShopSidebar'
import ProductTemplateNormal from '../components/product-templates/ProductTemplateNormal'
import FrontPagination from '../components/helpers/FrontPagination'
import { paginate } from '../helpers/functions'

export default {
  name: 'Shop',
  components: {
    FrontPagination,
    ProductTemplateNormal,
    ShopSidebar,
  },

  head() {
    return {
      title: 'Online Shop | Shop',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Shop Page',
        },
      ],
    }
  },
  computed: {
    categoriesTree() {
      return this.$store.state.general.categoriesTree
    },
    products() {
      return this.$store.state.general.shop.products
    },
    page() {
      return this.$store.state.general.shop.page
    },
  },

  mounted() {
    // reset shop filter
    this.$store.dispatch('general/resetShopFilter')

    if (this.$route.query.page) {
      this.$store.commit('general/setPage', this.$route.query.page)
    }

    if (this.$route.query.category_id) {
      this.$store.commit('general/setCategoryId', this.$route.query.category_id)

      // load brands by this category
      this.$store.dispatch(
        'general/fetchBrandsByCategory',
        this.$route.query.category_id
      )
    }

    if (this.$route.query.brand_id) {
      this.$store.commit('general/setBrand', this.$route.query.brand_id)
    }

    if (this.$route.query.from_price) {
      this.$store.commit('general/setFromPrice', this.$route.query.from_price)
    }

    if (this.$route.query.to_price) {
      this.$store.commit('general/setToPrice', this.$route.query.to_price)
    }

    this.$nextTick(() => {
      this.$nuxt.$loading.start()

      this.$store.dispatch('general/fetchShopProducts').then(() => {
        this.$nuxt.$loading.finish()
      })
    })
  },
  methods: {
    paginate(pageNumber) {
      paginate(this, pageNumber)
    },
  },
}
</script>

<style scoped>
.col-sm-4 {
  height: 437px !important;
  margin-bottom: 30px !important;
}
.no-products {
  color: #696763;
  font-size: 15px;
  font-family: 'Roboto', sans-serif;
}
</style>
