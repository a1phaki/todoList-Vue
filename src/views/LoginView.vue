<script setup>
import {RouterLink,useRouter} from 'vue-router';
import {ref} from 'vue';
import axios from 'axios';

const router = useRouter();

const loginData = ref({
  email:'jok.jok.123@gmail.com',
  password:'12345678'
})

const login = async ()=>{
  if(!loginData.value.email || !loginData.value.password) {
    alert('請填寫所有欄位');
    return;
  }
  try{
    const res = await axios.post('https://todolist-api.hexschool.io/users/sign_in', loginData.value);
    const {token, expired} = res.data;
    document.cookie = `hexToken=${token}; expires=${new Date(expired)}; path=/`;
    alert('登入成功！');
    router.push('/todoList');
  }catch(error){
    alert(error.response?.data?.message);
  }
}
</script>

<template>
  <div class="bg-yellow">
    <div class="conatiner loginPage vhContainer ">
    <div class="side">
      <a href="#"><img class="logoImg" src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png" alt=""></a>
      <img class="d-m-n" src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png" alt="workImg">
    </div>
    <div>
      <form class="formControls" action="index.html">
        <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
        <label class="formControls_label" for="email">Email</label>
        <input class="formControls_input" type="text" id="email" name="email" placeholder="請輸入 email" v-model="loginData.email" required>
        <label class="formControls_label" for="pwd">密碼</label>
        <input class="formControls_input" type="password" name="pwd" id="pwd" placeholder="請輸入密碼" v-model="loginData.password" required>
        <input class="formControls_btnSubmit" type="button" @click="login" value="登入">
        <RouterLink class="formControls_btnLink" to="/">註冊帳號</RouterLink>
      </form>
    </div>
  </div>
  </div>
</template>

