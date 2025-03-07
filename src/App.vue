<template>
  <OnlineShoping :products="products" />
</template>

<script>
import OnlineShoping from "./components/OnlineShoping.vue";
import products from "./products.js"; // Import the products array

export default {
  components: { OnlineShoping },
  data() {
    return {
      products: products, // Pass the products array to the component
    };
  },
};
</script>