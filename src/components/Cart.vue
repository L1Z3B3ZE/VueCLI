<template>
  <h1>Корзина</h1>
  <button class="btnBack" @click="toHomePage">Назад</button>
  <div class="home-container">
    <div v-if="products.length === 0">
      <p>Корзина пуста</p>
    </div>
    <div class="products" v-else v-for="product in products" :key="product.id">
      <h3>{{ product.name }}</h3>
      <p>{{ product.description }}</p>
      <p>{{ product.price }}руб.</p>
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

<style scoped>
.home-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  padding: 20px;
  gap: 15px;
}

.products {
  border: 1px solid #dee2e6;
  border-radius: 5px;
  padding: 20px;
  width: calc(45% - 40px);
  text-align: center;
}

.products p {
  margin: 0 0 10px;
}

.btn {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.btnBack {
  display: flex;
  margin: 0 auto;
  background-color: #6c757d;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.checkout-button {
  display: block;
  width: 300px;
  padding: 10px;
  margin: 20px auto;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

h1{
  text-align: center;
}
</style>