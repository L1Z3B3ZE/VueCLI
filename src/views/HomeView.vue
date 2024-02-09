<template>
  <header>
    <button @click="getCatalogProduct">Каталог</button>
    <div v-if="!isAuthenticated">
      <router-link to="/signup">Sign Up</router-link>
      <router-link to="/login">Login</router-link>
    </div>
    <div v-else>
      <button @click="logout">logout</button><br/>
      <router-link to="/cart">Cart</router-link>
      <router-link to="/order">Orders</router-link>
    </div>
  </header>
  <div v-for="product in products" :key="product.id" class="catalog">
    <div class="product">
      <p>{{ product.name }}</p>
      <p>{{ product.description }}</p>
      <p>{{ product.price }} руб.</p>
      <button v-if="isAuthenticated" @click="addToCart(product)">В корзину</button>
    </div>
  </div>

</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      url: "https://jurapro.bhuser.ru/api-shop",
      products: [],
      productsInCart: []
    }
  },
  methods:{
    async getCatalogProduct(){
      const response = await fetch(this.url + '/products',{
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      if (response.ok) {
        const result = await response.json();
        this.products = result.data
        console.log('Result: ', result)
      } else {
        this.error = "Ошибка";
        console.error(this.error);
      }
    },

    logout() {
      localStorage.removeItem('userToken');
      this.$router.push('/login');
    },

    async addToCart(product) {
      const response = await fetch(`${this.url}/cart/${product.id}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localStorage.getItem('userToken')}`
        },
      });
      if (response.ok) {
        const result = await response.json();
        console.log('Result: ', result)
        this.productsInCart.push(product);
      } else {
        this.error = "Ошибка при добавлении товара в корзину";
        console.error(this.error);
      }
    }
  },
  computed: {
    isAuthenticated() {
      return !!localStorage.getItem('userToken');
    }
  }
}
</script>
