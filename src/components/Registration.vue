<template>
  <div class="register">
    <form class="form" @submit.prevent="authorization">
      <label>FIO</label>
      <input type="text" required v-model="fio">
      <label>Email</label>
      <input type="email" required v-model="email">
      <label>Password</label>
      <input type="password" required v-model="password">
      <button type="submit">Зарегистрироваться</button>
    </form>
    <button @click="toHomePage">Назад</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      fio: '',
      email: '',
      password: ''
    }
  },
  methods: {
    async authorization() {
      const user = {
        fio: this.fio,
        email: this.email,
        password: this.password
      };

      const response = await fetch(this.url + '/signup', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(user)
      });

      if (response.ok) {
        this.$router.push('/login');
      } else {
        this.error = "Ошибка при регистрации";
        console.error('Ошибка:', this.error);
      }

      this.fio = '';
      this.email = '';
      this.password = '';
    },
    toHomePage(){
      this.$router.push('/');
    }
  },
}
</script>

<style scoped>
.register {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background-color: #f8f9fa;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 20px;
  border: 1px solid #dee2e6;
  border-radius: 5px;
}

.form label {
  font-weight: bold;
}

.form input {
  padding: 10px;
  border: 1px solid #dee2e6;
  border-radius: 5px;
}

.form button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.register > button {
  margin-top: 20px;
  padding: 10px;
  background-color: #6c757d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>
