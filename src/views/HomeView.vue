<template>
  <div id="home">
    <div id="background-div" class="page-holder bg-cover">
      <div class="container py-5">
        <header class="text-left text-white py-5">
<!--          <h3 class="mb-4 rounded" id="heading">-->
<!--            <a href="#start-shopping" class="bg-white px-2 py-2 rounded"> Start shopping</a>-->
<!--          </h3>-->
<!--          <p class="lead mb-0 bg-dark p-1 rounded"> Demo Ecommerce</p>-->
        </header>
      </div>
      <hr />
    </div>

    <div v-if="token != null" class="container">
      <div class="row">
        <div class="col-12 text-center">
          <h2 class="pt-3"> Recommendations</h2>
        </div>
      </div>
      <div class="row">
        <div v-for="index in this.recommendationsSize" :key="index"
             class="col-md-6 col-xl-4 col-12 pt-3 justify-content-around d-flex">
          <ProductBox :product="recommendations[index-1]" />
        </div>
      </div>
    </div>

    <!--    display categories-->
    <div class="container">
      <div class="row">
        <div class="col-12 text-center">
          <h2 class="pt-3">Categories</h2>
        </div>
      </div>
      <div class="row">
        <div v-for="index in this.categorySize" :key="index"
             class="col-md-6 col-xl-4 col-12 pt-3 justify-content-around d-flex">
          <CategoryBox :category="categories[index-1]" />
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import CategoryBox from "../components/category/CategoryBox";
import ProductBox from "../components/ProductBox";
import axios from "axios";
export default {
  name: "HomeView",
  components: {ProductBox, CategoryBox},
  props: ["categories", "products", "baseURL"],
  data() {
    return {
      categorySize: 0,
      productSize: 0,
      recommendations: null,
      recommendationsSize: 0,
      token: null,
    };
  },
  methods: {
    getRecommendations(){
      axios
          .get(`${this.baseURL}recs/get/?token=${this.token}`)
          .then((res) => {
            const result = res.data;
            this.recommendations = result;
            this.recommendationsSize = this.recommendations.length;
            console.log(this.recommendations);
              })
          .catch((err) => console.log("err", err));
    },
  },
  mounted() {
    this.categorySize = Math.min(6, this.categories.length );
    this.productSize = Math.min(8, this.products.length);
    this.token = localStorage.getItem("token");
    this.getRecommendations();
  },
};
</script>
<style>
.page-holder {
  min-height: 100vh;
}
.bg-cover{
  background-size: cover !important;
}
#background-div {
  background: url("../assets/home3.png");
}
#heading {
  font-weight: 400;
}
</style>