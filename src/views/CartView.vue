<template>
  <div class="container">
    <div class="row">
      <div class="col-12 text-center">
        <h3 class="pt-3">
          Shopping cart
        </h3>
      </div>
    </div>

    <!-- loop over the cart items and display -->

    <div
        v-for="cartItem in cartItems"
        :key="cartItem.id"
        class="row mt-2 pt-3 justify-content-around"
    >
      <div class="col-2"></div>
      <div class="col-md-3 embed-responsive embed-responsive-16by9">
        <img
            :src="cartItem.product.imageUrl"
            alt=""
            class="w-100 card-image-top embed-responsive-item"
            style="object-fit: cover"
        />
      </div>

      <!-- display product name, quantity -->
      <div class="col-md-5 px-3">
        <div class="card-block px-3">
          <h6 class="card-title">
            <router-link
                :to="{ name: 'ShowDetails', params: { id: cartItem.product.id } }"
            >
              {{ cartItem.product.name }}
            </router-link>
          </h6>

          <p class="mb-0 font-weight-bold" id="item-price">
            $ {{ cartItem.product.price }} per unit
          </p>
          <p class="mb-0" style="float:left">
            Quantity:{{ cartItem.quantity }}
          </p>
        </div>
        <p class="mb-0" style="float:right">
          Total:
          <span class="font-weight-bold">
            $ {{ cartItem.product.price * cartItem.quantity }}
          </span>
        </p>
        <br />
        <a href="#" class="text-right" @click="deleteItem(cartItem.id)"
        >Remove from cart
        </a>
      </div>
      <div class="col-2"></div>
      <div class="col-12"><hr /></div>
    </div>

    <div v-if="token != null" class="container">
      <div class="row">
        <div class="col-12 text-center">
          <h2 class="pt-3"> Recommendations based on your cart</h2>
        </div>
      </div>
      <div class="row">
        <div v-for="index in this.recommendationsSize" :key="index"
             class="col-md-6 col-xl-4 col-12 pt-3 justify-content-around d-flex">
          <ProductBox :product="recommendations[index-1]" />
        </div>
      </div>
    </div>

    <!-- display the price -->
    <div class="total-cost pt-2 text-right">
      <h5>Total : ${{ totalCost.toFixed(2) }}</h5>
      <button type="button" class="btn btn-primary confirm" @click="checkout">
        Confirm order
      </button>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      cartItems: [],
      token: null,
      totalCost: 0,
      recommendations: null,
      recommendationsSize: 0,
    };
  },
  props: ["baseURL"],
  methods: {
    // fetch All items in cart
    listCartItems() {
      axios
          .get(`${this.baseURL}cart/?token=${this.token}`)
          .then((res) => {
            const result = res.data;
            this.cartItems = result.cartItems;
            this.totalCost = result.totalCost;
          })
          .catch((err) => console.log("err", err));
    },
    deleteItem(itemId) {
      axios
          .delete(`${this.baseURL}cart/delete/${itemId}/?token=${this.token}`)
          .then((res) => {
            if (res.status == 200) {
              this.$router.go(0);
            }
          })
          .catch((err) => console.log("err", err));
    },
    getRecommendations(){
      axios
          .get(`${this.baseURL}recs/getCartRecs/?token=${this.token}`)
          .then((res) => {
            const result = res.data;
            this.recommendations = result;
            this.recommendationsSize = this.recommendations.length;
            console.log(this.recommendations);
          })
          .catch((err) => console.log("err", err));
    },
    checkout() {
      this.$router.push({ name: 'Checkout' });
    },
  },
  mounted() {
    this.token = localStorage.getItem("token");
    this.listCartItems();
    this.getRecommendations();
  },
};
</script>