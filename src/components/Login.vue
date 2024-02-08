<template>
  <div class="login">
    <form class="form" @submit.prevent="login">
      Email<input type="text" v-model="email">
      Password<input type="password" v-model="password">
      <button type="submit">Войти</button>
    </form>
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
    }
  }
}
</script>