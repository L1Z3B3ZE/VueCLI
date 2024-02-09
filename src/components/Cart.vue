<template>
  <h1>Корзина</h1>
  <button @click="toHomePage">Назад</button>
  <div class="home-container">
    <div v-if="products.length === 0">
      <p>Корзина пуста</p>
    </div>
    <div v-else v-for="product in products" :key="product.id">
      <p>Название: {{ product.name }}</p>
      <p>Описание: {{ product.description }}</p>
      <p>Цена: {{ product.price }}руб.</p>
      <button @click="deleteProduct(product)" type="submit" class="btn">Удалить из корзины</button>
    </div>
  </div>
  <button @click="userOrders" v-if="products.length > 0" class="checkout-button">Оформить заказ</button>
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
    toHomePage(){
      this.$router.push('/');
    },
    async deleteProduct(product) {
      const token = localStorage.getItem('userToken');
      if (!token) {
        console.error('Токен пользователя отсутствует.');
        return;
      }

      try {
        const response = await fetch(`${this.url}/cart/${product.id}`, {
          method: "DELETE",
          headers: {
            "Authorization": `Bearer ${token}`
          }
        });
        if (response.ok) {
          console.log("Товар успешно удален из корзины");
          location. reload()

        } else {
          console.error("Ошибка удаления товара из корзины:", response.statusText);
        }
      } catch (error) {
        console.error("Ошибка удаления товара из корзины:", error);
      }
    },
    async userOrders() {
      const token = localStorage.getItem('userToken');
      if (!token) {
        console.error('Токен пользователя отсутствует.');
        return;
      }

      const url = "https://jurapro.bhuser.ru/api-shop/order";
      try {
        const response = await fetch(url, {
          method: "POST",
          headers: {
            "Authorization": `Bearer ${token}`
          }
        });

        if (response.ok) {
          const data = await response.json();
          console.log(data.data.message);
          this.$router.push('/order');
        } else if (response.status === 422) {
          const errorData = await response.json();
          console.error("Ошибка оформления заказа:", errorData.error.message);
        } else {
          console.error("Ошибка оформления заказа:", response.statusText);
        }
      } catch (error) {
        console.error("Ошибка оформления заказа:", error);
      }
    },
  }
};
</script>