<template>
  <section class="app-container">
    <h1 class="title">
      Membership Builder
    </h1>
    <div class="description">
      <div class="description-text">
        Check out these great options!
      </div>
    </div>
    <div>
      <loading :active.sync="isLoading"></loading>
      <products @go-next="$emit('go-next')" v-if="this.products.length" :products="products" />
      <div v-if="!this.products.length">
        No suitable products was found.
      </div>
    </div>
  </section>
</template>

<script>
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import Products from '../components/Products'

export default {
  mounted() {
    this.isLoading = true;
    let family = this.$store.state.family;
    let family_option = ''
    for (let type_uuid in family) {
      let count = family[type_uuid].count;
      let term_id = family[type_uuid].term_id;
      family_option += term_id + '_' + count + ';';
    }
    window.jQuery.ajax({
      url: '/memberships/api/products/' + (this.$store.state.location ? this.$store.state.location : '') + '/' + family_option,
      dataType: 'json',
      data: {}
    }
    ).then((data)=>{
      this.isLoading = false
      this.products = Object.keys(data).map(product => data[product]);
    }).catch(() => {
      this.isLoading = false
    })
  },
  components: {
    Loading,
    Products
  },
  data () {
    return {
      isLoading: false,
      products: []
    }
  }
}
</script>
