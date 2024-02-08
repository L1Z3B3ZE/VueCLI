<template>
  <header>
    <h1 class="catalog" @click="getCatalogProduct">Каталог</h1>
    <router-link to="/signup">Sign Up</router-link>
  </header>
  <div v-for="product in products" :key="product.id" class="catalog">
    <div class="product">
      <p>{{ product.name }}</p>
      <p>{{ product.description }}</p>
      <p>{{ product.price }} руб.</p>
      <button>В корзину</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      url: "https://jurapro.bhuser.ru/api-shop/products",
      products: [],
    }
  },
  methods:{
    async getCatalogProduct(){
      const response = await fetch(this.url,{
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
    }
  }
}
</script>
