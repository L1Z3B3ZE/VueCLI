<template>
  <div class="user-orders">
    <h2>Оформленные заказы</h2>
    <div v-if="orders.length === 0">
      <p>У вас пока нет оформленных заказов.</p>
    </div>
    <div v-else>
      <div v-for="order in orders" :key="order.id" class="order-item">
        <h3>Заказ №{{ order.id }}</h3>
        <p>Список товаров: {{ order.products.join(', ') }}</p>
        <p>Общая стоимость заказа: {{ order.order_price }}</p>
      </div>
    </div>
    <button @click="toHomePage" class="back-button">Назад</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      orders: []
    };
  },
  created() {
    this.Orders();
  },
  methods: {
    async Orders() {
      const token = localStorage.getItem('userToken');
      if (!token) {
        console.error('Токен пользователя отсутствует.');
        return;
      }
      try {
        const response = await fetch(this.url + '/order', {
          headers: {
            "Authorization": `Bearer ${token}`
          }
        });
        if (response.ok) {
          const data = await response.json();
          this.orders = data.data;
        } else {
          console.error("Ошибка получения списка оформленных заказов");
        }
      } catch (error) {
        console.error("Ошибка загрузки данных:", error);
      }
    },
    toHomePage(){
      this.$router.push('/');
    }
  }
};
</script>