<template>
  <h1>Создайте аккаунт</h1>
  <p><input type="text" placeholder="Email" v-model="email" /></p>
  <p><input type="password" placeholder="Password" v-model="password" /></p>
  <p><button @click="register">Зарегистрироваться</button></p>
  <p><button @click="signInWithGoogle">Авторизация через Гугл</button></p>
</template>
<script setup>
import { ref } from 'vue'
import { getAuth, createUserWithEmailAndPassword } from 'firebase/auth'
import { useRouter } from 'vue-router'
const email = ref('')
const password = ref('')
const router = useRouter()

const register = () => {
  createUserWithEmailAndPassword(getAuth(), email.value, password.value)
    .then((data) => {
      console.log('Регистрация прошла успешно!')
      router.push('/feed')
    })
    .catch((error) => {
      console.log(error.code)
      alert(error.message)
    })
}
</script>
