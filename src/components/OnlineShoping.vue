<template>
  <div class="container">
    <!-- Navbar -->
    <div class="align-items-baseline bg-dark w-60 p-3 my-2 text-light d-flex justify-content-between">
      <a href="#" style="color: yellow; text-decoration: none" @click.prevent="iscartvisible = false">Products</a>
      <div class="d-flex align-items-baseline">
        <p class="px-2">
          {{ cart.items.length }} <span v-if="cart.items.length === 1">Item: </span><span v-else>Items: </span>
          {{ formatCurrency(totalPrice() + totalPrice() * 0.14) }}
        </p>
        <button class="btn btn-primary" @click="iscartvisible = true">Show Cart</button>
      </div>
    </div>

    <!-- Products -->
    <div class="justify-content-center row" v-if="!iscartvisible">
      <div v-for="product in products" :key="product.id" class="card" style="width: 23rem; margin: 0.2rem">
        <img :src="product.image" :title="product.name" />
        <h5 class="w-100 text-center my-2">{{ product.name }}</h5>
        <p style="text-align: justify">{{ product.description }}</p>
        <div class="card-footer">
          <div class="d-flex justify-content-between align-items-baseline">
            <p class="mx-1">
              <span :class="stockClass(product.instock)">Instock: {{ product.instock }}</span>
            </p>
            <button class="btn btn-primary" :disabled="product.instock === 0" @click="addToCart(product)">
              Add To Cart
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Cart -->
    <div v-if="iscartvisible">
      <h4 class="w-100 text-center text-danger py-2" v-if="cart.items.length === 0">
        Sorry, No Items found, Please Add!!!
      </h4>
      <table v-else class="table table-striped text-center table-bordered">
        <thead>
          <tr>
            <td>ID</td>
            <td>Name</td>
            <td>Price</td>
            <td>Quantity</td>
            <td>Total Price</td>
            <td>Actions</td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in cart.items" :key="item.product.id">
            <td>{{ item.product.id }}</td>
            <td>{{ item.product.name }}</td>
            <td>{{ formatCurrency(item.product.price) }}</td>
            <td>{{ item.quantity }}</td>
            <td>{{ formatCurrency(item.product.price * item.quantity) }}</td>
            <td>
              <button class="btn btn-primary mx-1" @click="increaseQuantity(item)" :disabled="item.product.instock === 0">+</button> |
              <button class="mx-1 btn btn-danger" @click="decreaseQuantity(item)">-</button>
            </td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="3">Total Prices</td>
            <td colspan="3">{{ formatCurrency(totalPrice()) }}</td>
          </tr>
          <tr>
            <td colspan="3">Total Taxes</td>
            <td colspan="3">{{ formatCurrency(totalPrice() * 0.14) }}</td>
          </tr>
          <tr>
            <td colspan="3">Total</td>
            <td colspan="3">{{ formatCurrency(totalPrice() + totalPrice() * 0.14) }}</td>
          </tr>
        </tfoot>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    products: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      iscartvisible: false,
      cart: { items: [] },
    };
  },
  methods: {
    addToCart(product) {
      if (!this.isProductExist(product)) {
        this.cart.items.push({ product: product, quantity: 1 });
      } else {
        this.cart.items.find((item) => item.product.id === product.id).quantity++;
      }
      product.instock--;
    },
    totalPrice() {
      return this.cart.items.reduce((sum, item) => sum + item.quantity * item.product.price, 0);
    },
    isProductExist(product) {
      return this.cart.items.some((item) => item.product.id === product.id);
    },
    increaseQuantity(item) {
      if (item.product.instock > 0) {
        item.quantity++;
        item.product.instock--;
      }
    },
    decreaseQuantity(item) {
      item.quantity--;
      item.product.instock++;
      if (item.quantity === 0) {
        this.cart.items = this.cart.items.filter((i) => i.product.id !== item.product.id);
      }
    },
    formatCurrency(value) {
      return new Intl.NumberFormat("en-US", {
        style: "currency",
        currency: "USD",
        minimumFractionDigits: 2,
      }).format(value);
    },
    stockClass(instock) {
      return instock >= 5 ? "more" : instock < 5 && instock > 0 ? "less" : "none";
    },
  },
};
</script>

<style scoped>
.more {
  color: green;
}
.less {
  color: orange;
}
.none {
  color: red;
}
.more,
.less,
.none {
  font-weight: bold;
}
</style>