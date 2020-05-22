<template>
  <div class="product">
    <Header />
    <div class="breacrumb-section">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text product-more">
              <router-link to="/">
                <i class="fa fa-home"></i> Home
              </router-link>
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
                  <img class="product-big-img" :src="thumbsholder" alt />
                </div>
                <div class="product-thumbs" v-if="productDetails.galleries.length>0">
                  <carousel :nav="false" :dots="false" class="product-thumbs-track ps-slider">
                    <div
                      v-for="images in productDetails.galleries"
                      :key="images.id"
                      class="pt"
                      @click="changeImage(images.photo)"
                      :class="images.photo == thumbsholder ? 'active': ''"
                    >
                      <img :src="images.photo" alt />
                    </div>
                  </carousel>
                </div>
              </div>
              <div class="col-lg-6">
                <div class="product-details">
                  <div class="pd-title">
                    <span>{{productDetails.type}}</span>
                    <h3>{{productDetails.name}}</h3>
                  </div>
                  <div class="pd-desc">
                    <p v-html="productDetails.description"></p>
                    <h4>${{productDetails.price}}</h4>
                  </div>
                  <div class="quantity">
                    <a
                      @click="saveKeranjang(productDetails.id, productDetails.name,productDetails.galleries[0].photo,productDetails.price)"
                      href="#"
                      class="primary-btn pd-cart"
                    >Add To Cart</a>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Product Shop Section End -->
    <RelatedProduct />
    <Footer />
  </div>
</template>

<script>
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";
import RelatedProduct from "@/components/RelatedProduct.vue";
import axios from "axios";

import carousel from "vue-owl-carousel";

export default {
  name: "Product",
  components: {
    Header,
    Footer,
    carousel,
    RelatedProduct
  },

  data() {
    return {
      thumbsholder: "",
      productDetails: [],
      keranjangUser: []
    };
  },

  methods: {
    changeImage(urlImage) {
      this.thumbsholder = urlImage;
    },
    setDataPicture(data) {
      this.productDetails = data;
      this.thumbsholder = data.galleries[0].photo;
    },
    saveKeranjang(idProduct, nameProduct, photoProduct, priceProduct) {
      var dataStorage = {
        id: idProduct,
        name: nameProduct,
        harga: priceProduct,
        photo: photoProduct
      };

      this.keranjangUser.push(dataStorage);
      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem("keranjangUser", parsed);

      window.location.reload();
    }
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
      .get("http://shayna-backend.belajarkoding.com/api/products", {
        params: {
          id: this.$route.params.id
        }
      })
      .then(res => this.setDataPicture(res.data.data))
      .catch(err => console.log(err));
  }
};
</script>

<style scoped>
.ps-slider .pt {
  margin-right: 10px;
}
.ps-slider .pt img {
  max-height: 200px;
}
</style>