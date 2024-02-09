<template>
  <div class="register">
    <form class="form" @submit.prevent="authorization">
      <label>ФИО</label>
      <input type="text" v-model="fio" :class="{ 'error': fioError }">
      <p v-if="fioError" class="error-text">Введите корректное ФИО</p>
      <label>Email</label>
      <input type="email" v-model="email" :class="{ 'error': emailError }">
      <p v-if="emailError" class="error-text">Введите корректный Email</p>
      <label>Пароль</label>
      <input type="password" v-model="password" :class="{ 'error': passwordError }">
      <p v-if="passwordError" class="error-text">Пароль должен содержать 8 символов</p>
      <button type="submit">Зарегистрироваться</button>
    </form>
    <button @click="toHomePage">Назад</button>
    <div class="popup" v-if="showPopup">
      Регистрация прошла успешно
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: 'https://jurapro.bhuser.ru/api-shop',
      fio: '',
      email: '',
      password: '',
      fioError: false,
      emailError: false,
      passwordError: false,
      showPopup: false
    }
  },
  methods: {
    async authorization() {
      this.fioError = !this.fio;
      this.emailError = !this.email.includes('@');
      this.passwordError = this.password.length < 8;

      if (this.fioError || this.emailError || this.passwordError) {
        return;
      }

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
        this.showPopup = true;
        setTimeout(() => {
          this.showPopup = false;
        }, 1000);
        setTimeout(()=> {
          this.$router.push('/login');
        }, 1000)
      } else {
        this.error = "Ошибка при регистрации";
        console.error('Ошибка:', this.error);
      }

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
  background-color: #625580;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.form button:hover{
  background-color: #371b59;
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
.register > button:hover{
  background-color: #4f575d;
}

.error {
  box-shadow: 0px 0px 5px red;
}

.error-text {
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
