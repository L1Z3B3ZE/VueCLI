<template>
  <div class="login">
    <form class="form" @submit.prevent="login">
      Email<input type="text" v-model="email">
      Password<input type="password" v-model="password">
      <button type="submit">Войти</button>
    </form>
    <button @click="toHomePage">Назад</button>
    <p v-if="error">{{ error }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      email: '',
      password: '',
      error: ''
    }
  },
  methods: {
    async login() {
      const user = {
        email: this.email,
        password: this.password
      };


      const response = await fetch(this.url + '/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(user)
      });

      if (response.ok) {
        const userToken = await response.json();
        localStorage.setItem('userToken', userToken.data.user_token);
        this.$router.push('/');
      } else {
        this.error = "Неверные учетные данные";
      }
    },
    toHomePage(){
      this.$router.push('/');
    }
  }
}
</script>

<style scoped>
.login {
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

.login > button {
  margin-top: 20px;
  padding: 10px;
  background-color: #6c757d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>