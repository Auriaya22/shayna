<template>
  <div class="shopping">
    <HeaderShayna />

    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text product-more text-left">
              <router-link to="/"><i class="fa fa-home"></i> Home</router-link>
              <span>Shopping Cart</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Shopping Cart Section Begin -->
    <section class="shopping-cart spad">
      <div class="container">
        <div class="row">
          <div class="col-lg-8">
            <div class="row">
              <div class="col-lg-12">
                <div class="cart-table">
                  <table>
                    <thead>
                      <tr>
                        <th>Image</th>
                        <th class="p-name text-center">Product Name</th>
                        <th>Price</th>
                        <th>Action</th>
                      </tr>
                    </thead>
                    <tbody v-if="keranjangUser.length > 0">
                      <tr v-for="(barang, index) in keranjangUser" :key="index">
                        <td class="cart-pic first-row">
                          <img :src="barang.photo" class="img-fluid photo-item"/>
                        </td>
                        <td class="cart-title first-row text-center">
                          <h5>{{ barang.name }}</h5>
                        </td>
                        <td class="p-price first-row">
                          Rp. {{ barang.price }}
                        </td>
                        <td class="delete-item" @click="removeItem(index)">
                          <a href="#">
                            <i class="material-icons"> close </i>
                          </a>
                        </td>
                      </tr>
                    </tbody>
                    <tbody v-else>
                      <tr>
                        <td colspan="4"><p>Keranjang masih kosong</p></td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
              <div class="col-lg-8">
                <h4 class="mb-4 text-left">Informasi Pembeli:</h4>
                <div class="user-checkout">
                  <form>
                    <div class="form-group text-left">
                      <label for="namaLengkap">Nama lengkap</label>
                      <input
                        type="text"
                        class="form-control"
                        id="namaLengkap"
                        aria-describedby="namaHelp"
                        placeholder="Masukan Nama"
                        v-model="customerInfo.name"
                      />
                    </div>
                    <div class="form-group text-left">
                      <label for="namaLengkap">Email Address</label>
                      <input
                        type="email"
                        class="form-control"
                        id="emailAddress"
                        aria-describedby="emailHelp"
                        placeholder="Masukan Email"
                        v-model="customerInfo.email"
                      />
                    </div>
                    <div class="form-group text-left">
                      <label for="noHP">No. HP</label>
                      <input
                        type="text"
                        class="form-control"
                        id="noHP"
                        aria-describedby="noHPHelp"
                        placeholder="Masukan No. HP"
                        v-model="customerInfo.number"
                      />
                    </div>
                    <div class="form-group text-left">
                      <label for="alamatLengkap">Alamat Lengkap</label>
                      <textarea
                        class="form-control"
                        id="alamatLengkap"
                        rows="3"
                        v-model="customerInfo.address"
                      ></textarea>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
          <div class="col-lg-4">
            <div class="row">
              <div class="col-lg-12">
                <div class="proceed-checkout">
                  <ul>
                    <li class="subtotal text-left">
                      ID Transaction <span>#SH12000</span>
                    </li>
                    <li class="subtotal text-left mt-3">
                      Subtotal
                      <span>Rp. {{ totalHarga }}</span>
                    </li>
                    <li class="subtotal text-left mt-3">
                      Pajak 10% <span>Rp. {{ ditambahPajak }}</span>
                    </li>
                    <li class="subtotal text-left mt-3">
                      Total Biaya
                      <span
                        >Rp. {{ totalBiaya }}</span
                      >
                    </li>
                    <li class="subtotal text-left mt-3">
                      Bank Transfer <span>Mandiri</span>
                    </li>
                    <li class="subtotal text-left mt-3">
                      No. Rekening <span>2208 1996 1403</span>
                    </li>
                    <li class="subtotal text-left mt-3">
                      Nama Penerima <span>Shayna</span>
                    </li>
                  </ul>
                    <a href="#" class="proceed-btn" @click="checkout()">
                      I ALREADY PAID
                    </a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Shopping Cart Section End -->

    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
import HeaderShayna from "@/components/HeaderShayna.vue";
import FooterShayna from "@/components/FooterShayna.vue";

import axios from "axios";

export default {
  name: "ShoppingCardView",
  components: {
    HeaderShayna,
    FooterShayna,
  },
  data() {
    return {
      keranjangUser: [],
      customerInfo: {
        name    : '',
        email   : '',
        number  : '',
        address : ''
      }
    };
  },
  methods: {
    removeItem(index) {
      let keranjangUserStorage = JSON.parse(localStorage.getItem("keranjangUser"));
      let itemKeranjangUserStorage = keranjangUserStorage.map(itemKeranjangUserStorage => itemKeranjangUserStorage.id);

      let idx = itemKeranjangUserStorage.findIndex(id => id == index);
      this.keranjangUser.splice(idx, 1);

      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem('keranjangUser', parsed);

      location.reload();
    },
    // mengirimkan data checkout ke API checkout
    checkout(){
      let producIds = this.keranjangUser.map(function(product){
        return product.id
      });

      let checkoutData = {
        'name': this.customerInfo.name,
        'email': this.customerInfo.email,
        'number': this.customerInfo.number,
        'address': this.customerInfo.address,
        'transaction_total': this.totalHarga,
        'transaction_status': "PENDING",
        'transaction_details': producIds,
      };

      // console.log(checkoutData)

      axios
      .post('http://shayna-backend.test/api/checkout', checkoutData)
      .then(() => this.$router.push('success'))
      // eslint-disable-next-line no-console
      .catch(err => console.log(err));
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
  },
  computed: {
    totalHarga(){
      return this.keranjangUser.reduce(function(item, data){
        return item + data.price;
      }, 0);
    },
    ditambahPajak(){
      return (this.totalHarga / 10);
    },
    totalBiaya(){
      return this.totalHarga + this.ditambahPajak;
    }
  }
};
</script>
<style scoped>
  .photo-item{
    width: 80px;
    height: 80px;
  }
</style>