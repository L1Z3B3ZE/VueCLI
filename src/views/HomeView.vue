<template>
  <header class="header">
    <a href="#" @click="getCatalogProduct">Каталог</a>
    <div class="headerNav" v-if="!isAuthenticated">
      <router-link to="/signup">Зарегистрироваться</router-link>
      <router-link to="/login">Войти</router-link>
    </div>
    <div class="headerNav" v-else>
      <router-link to="/cart">Корзина</router-link>
      <router-link to="/order">Оформленные заказы</router-link>
      <a href="#" @click="logout">Выход</a>
    </div>
  </header>

  <div class="catalog">
    <transition-group class="catalog" name="list" tag="div">
      <div v-for="product in products" :key="product.id" class="product">
        <h3>{{ product.name }}</h3>
        <p>{{ product.description }}</p>
        <p>{{ product.price }} руб.</p>
        <button v-if="isAuthenticated" @click="addToCart(product)">В корзину</button>
      </div>
    </transition-group>
  </div>
  <div class="popup" v-if="showPopup">
    Товар добавлен в корзину
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      url: "https://jurapro.bhuser.ru/api-shop",
      products: [],
      productsInCart: [],
      showPopup: false,
      showProducts: false,
    }
  },
  methods:{
    async getCatalogProduct(){
      this.showProducts = !this.showProducts;
      if (this.showProducts) {
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
      } else {
        this.products = [];
      }
    },

    logout() {
      localStorage.removeItem('userToken');
      this.$router.push('/');
      location. reload()
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
        this.showPopup = true;
        setTimeout(() => {
          this.showPopup = false;
        }, 1000);
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

<style scoped>

a {
  text-decoration: none;
  color: black;
}

a:hover {
  border-bottom: 1px solid black;
}

.header {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  background-color: #f8f9fa;
}

.headerNav {
  display: flex;
  gap: 10px;
}

.catalog {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  padding: 20px;
  justify-content: space-around;

}

.product {
  border: 1px solid #dee2e6;
  border-radius: 5px;
  padding: 20px;
  width: calc(33% - 40px);
  text-align: center;
  box-shadow: 0 5px 10px #e7e7e7;
  opacity: 0;
  animation: fadeIn 1s ease-in forwards;
}
.product:hover{
  box-shadow: 0 8px 40px 0 #d0d0d0;
}

@keyframes fadeIn {
  to {
    opacity: 1;
  }
}

.product p {
  margin: 0 0 10px;
}

.product button {
  background-color: #625580;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.product button:hover{
  background-color: #371b59;
}

.popup {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #625580;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  z-index: 999;
  opacity: 70%;
}

.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(-30px);
}
</style>


