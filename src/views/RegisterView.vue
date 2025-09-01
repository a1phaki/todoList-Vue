<script setup>
import {RouterLink} from 'vue-router';
import {ref} from 'vue';
import axios from 'axios';

const memberData = ref({
  email:'',
  password:'',
  nickname:''
})

const register = async ()=>{
  if(!memberData.value.email || !memberData.value.password || !memberData.value.nickname || !document.getElementById('pwdCheck').value) {
    alert('請填寫所有欄位');
    return;
  }

  if(memberData.value.password !== document.getElementById('pwdCheck').value) {
    alert('密碼輸入不一致');
    return;
  }
  try{
    await axios.post('https://todolist-api.hexschool.io/users/sign_up', memberData.value);
    alert('恭喜您註冊成功！');
  }catch(error){
    alert(error.response.data.message);
  }
}


</script>

<template>
  <div class="bg-yellow">
    <div class="conatiner signUpPage vhContainer">
      <div class="side">
        <a href="#"><img class="logoImg" src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png" alt=""></a>
        <img class="d-m-n" src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png" alt="workImg">
      </div>
      <div>
        <form class="formControls" action="index.html">
          <h2 class="formControls_txt">註冊帳號</h2>
          <label class="formControls_label" for="email">Email</label>
          <input class="formControls_input" type="text" id="email" name="email" placeholder="請輸入 email" v-model="memberData.email" required>
          <label class="formControls_label" for="name">您的暱稱</label>
          <input class="formControls_input" type="text" name="name" id="name" placeholder="請輸入您的暱稱" v-model="memberData.nickname" required>
          <label class="formControls_label" for="pwd">密碼</label>
          <input class="formControls_input" type="password" name="pwd" id="pwd" placeholder="請輸入密碼" v-model="memberData.password" required>
          <label class="formControls_label" for="pwd">再次輸入密碼</label>
          <input class="formControls_input" type="password" name="pwd" id="pwdCheck" placeholder="請再次輸入密碼" required>
          <input class="formControls_btnSubmit" type="button" @click="register" value="註冊帳號">
          <RouterLink class="formControls_btnLink" to="/login">登入</RouterLink>
        </form>
      </div>
    </div>
  </div>
</template>

