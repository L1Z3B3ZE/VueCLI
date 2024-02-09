<template>
  <div class="login">
    <form class="form" @submit.prevent="login">
      <label>Email</label>
      <input type="text" v-model="email" :class="{'error': this.error}">
      <label>Пароль</label>
      <input type="password" v-model="password" :class="{'error': this.error}">
      <p class="error_text" v-if="error">{{ error }}</p>
      <button type="submit">Войти</button>
    </form>

    <button @click="toHomePage">Назад</button>
    <div class="popup" v-if="showPopup">
      авторизация прошла успешно
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      email: '',
      password: '',
      error: '',
      showPopup: false
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
        this.showPopup = true;
        setTimeout(() => {
          this.showPopup = false;
        }, 1000);
        setTimeout(()=> {
          this.$router.push('/')
        }, 1000)

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
  background-color: #625580;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.form button:hover{
  background-color: #371b59;
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
.login > button:hover{
  background-color: #4f575d;
}

.error {
  box-shadow: 0 0 5px red;
}

.error_text{
  color: red;
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
</style>