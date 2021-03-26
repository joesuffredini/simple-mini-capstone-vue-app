<template>
  <div class="home">
    <h1>The Products !</h1>

    <!-- Create Button -->
    <div>
      <h2>Create New Products for the Database</h2>
      Product:
      <input type="text" v-model="newProductTitle" />
      <br />
      Description:
      <input type="text" v-model="newProductDesc" />
      <br />
      Price:
      <input type="text" v-model="newProductPrice" />
      <br />
      Image_URL:
      <br />
      <input type="text" v-model="newProductImageUrl" />
    </div>

    <br />
    <button v-on:click="createProduct()">Create</button>

    <div>
      <div v-for="product in products" v-bind:key="product.id">
        <h3>{{ product.name }}</h3>
        <img v-bind:src="product.image_url" v-bind:alt="product.name" />
        <p>{{ product.price }}</p>
        <button v-on:click="showProduct(product)">Click for more info</button>
      </div>

      <!-- Modal -->

      <dialog id="product-details">
        <form method="dialog">
          <h1>Product info</h1>
          <p>
            Name:
            <input type="text" v-model="currentProduct.name" />
          </p>
          <br />
          <p>
            Description:
            <input type="text" v-model="currentProduct.description" />
          </p>
          <p>
            Price:
            <input type="text" v-model="currentProduct.price" />
          </p>
          Image_URL:
          <input type="text" v-model="currentProduct.image_url" />
          <p>
            <br />
            <button v-on:click="updateProduct(currentProduct)">Update</button>
            <button v-on:click="deleteProduct(currentProduct)">Delete</button>
            <button>Exit</button>
          </p>
        </form>
      </dialog>
    </div>
  </div>
</template>
<style></style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      products: [],
      newProductTitle: "",
      newProductDesc: "",
      newProductPrice: "",
      newProductImageUrl: "",
      currentProduct: {},
    };
  },

  created: function () {
    this.indexProducts();
  },

  methods: {
    indexProducts: function () {
      axios.get("/api/products").then((response) => {
        this.products = response.data;
        console.log(this.products);
      });
    },
    createProduct: function () {
      console.log("Create Product");
      var params = {
        name: this.newProductTitle,
        description: this.newProductDesc,
        price: this.newProductPrice,
        image_url: this.newProductImageUrl,
      };
      axios
        .post("/api/products", params)
        .then((response) => {
          console.log(response.data);
          this.products.push(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    showProduct: function (product) {
      this.currentProduct = product;
      console.log(product);
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function (product) {
      var parms = {
        name: product.name,
        description: product.description,
        price: product.price,
        image: product.image_url,
      };
      axios.patch("/api/products/" + product.id, parms).then((response) => {
        console.log("Success!", response.data);
      });
    },
    deleteProduct: function (product) {
      axios.delete("/api/products/" + product.id).then((response) => {
        console.log("Sucess!", response.data);
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>
