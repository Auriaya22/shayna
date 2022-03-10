<template>
  <div class="product">
    <HeaderShayna />

    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text product-more text-left">
              <router-link to="/"><i class="fa fa-home"></i> Home</router-link>
              <span>Detail</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Product Shop Section Begin -->
    <section class="product-shop spad page-details">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="row">
              <div class="col-lg-6">
                <div class="product-pic-zoom">
                  <img class="product-big-img" :src="gambar_default" alt="" />
                </div>
                <div class="product-thumbs" v-if="galleries.length > 0">
                  <carousel
                    class="product-thumbs-track ps-slider"
                    :loop="true"
                    :autoplay="true"
                    :items="galleries.length"
                    :dots="false"
                    :nav="false"
                  >
                    <div
                      v-for="ss in galleries"
                      :key="ss.id"
                      class="pt"
                      @click="changeImage(ss.photo)"
                      :class="ss.photo == gambar_default ? 'active' : ''"
                    >
                      <img :src="ss.photo" class="rounded img-thumbnail" />
                    </div>
                  </carousel>
                </div>
                <div v-else>
                  <p>Belum ada gambar produk</p>
                </div>
              </div>
              <div class="col-lg-6">
                <div class="product-details">
                  <div class="pd-title">
                    <span>{{ product.type }}</span>
                    <h3>{{ product.name }}</h3>
                  </div>
                  <div class="pd-desc">
                    <p v-html="product.description"></p>
                    <h4>Rp. {{ product.price }}</h4>
                  </div>
                  <div class="quantity">
                    <router-link to="/cart">
                    <a
                      @click="simpanKeranjang(product.id, product.name, product.price, galleries[0].photo)"
                      href="#"
                      class="primary-btn pd-cart"
                      >Add To Cart</a
                    >
                    </router-link>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Product Shop Section End -->

    <RelatedShayna />

    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
import HeaderShayna from "@/components/HeaderShayna.vue";
import RelatedShayna from "@/components/RelatedShayna.vue";
import FooterShayna from "@/components/FooterShayna.vue";

import axios from "axios";
import carousel from "vue-owl-carousel";

export default {
  name: "ProductView",
  components: {
    HeaderShayna,
    FooterShayna,
    RelatedShayna,
    carousel,
  },
  data() {
    return {
      gambar_default: "",
      product: [],
      galleries: [],
      keranjangUser: [],
    };
  },
  mounted() {
    if (localStorage.getItem("keranjangUser")) {
      try {
        this.keranjangUser = JSON.parse(localStorage.getItem("keranjangUser"));
      } catch (e) {
        localStorage.removeItem("keranjangUser");
      }
    }
    axios
      .get("http://shayna-backend.test/api/products", {
        params: {
          id: this.$route.params.id,
        },
      })
      .then((res) => {
        this.setDataPicture(res.data.data);
      })
      .catch((err) => console.log(err));
  },
  methods: {
    changeImage(urlImage) {
      this.gambar_default = urlImage;
    },
    setDataPicture(data) {
      this.product = data;
      this.galleries = data.galleries;
      this.gambar_default = data.galleries[0].photo;
    },
    simpanKeranjang(idProduct, nameProduct, priceProduct, photoProduct) {
      var productStored = {
        "id"    : idProduct,
        "name"  : nameProduct,
        "price" : priceProduct,
        "photo" : photoProduct
      };
      this.keranjangUser.push(productStored);
      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem("keranjangUser", parsed);
      location.reload();
    },
  },
};
</script>
<style scoped>
.product-thumbs .pt {
  margin-right: 10px;
}
</style>