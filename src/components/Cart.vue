<template>
  <div class="home-container">
    <div v-for="product in products" :key="product.id">
      <p>Название: {{ product.name }}</p>
      <p>Описание: {{ product.description }}</p>
      <p>Цена: {{ product.price }}руб.</p>
      <button @click="deleteProduct(product)" type="submit" class="btn">Удалить из корзины</button>
    </div>
  </div>
</template>


<script>
export default {
  name: 'cart',
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      products: [],
    };
  },
  created() {
    this.getProduct();
  },
  methods: {
    async getProduct() {
      const localToken = localStorage.getItem('userToken')
      const response = await fetch(this.url + '/cart', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localToken}`
        },
      })
      if (response.ok) {
        const result = await response.json();
        this.products = result.data
        console.log('Result: ', result)
      } else {
        this.error = "Ошибка";
        console.error(this.error);
      }
    },
  }
};
</script>