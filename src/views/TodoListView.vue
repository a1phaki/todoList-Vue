<script setup>
import { useRouter } from 'vue-router';
import { onMounted,ref,computed } from 'vue';
import axios from 'axios';

const router = useRouter();

const todoListData = ref([]);
const filterStatus = ref('all');
const nickname = ref('');
const todo = ref({
  content:'',
});

const token = getCookie('hexToken');

const completedTodos = ref(0);

function getCookie(name) {
  const value = `; ${document.cookie}`;
  const parts = value.split(`; ${name}=`);
  if (parts.length === 2) return parts.pop().split(';').shift();
  return null;
}

const loginCheck = async()=>{
  if (!token) {
    alert('您尚未登入，即將為你轉移到登入頁面');
    router.push('/login');
    return;
  }
  try{
    const res = await axios.get('https://todolist-api.hexschool.io/users/checkout',{
      headers:{
        Authorization:token
      }
    })
    console.log(res);
    nickname.value = res.data.nickname;
  }catch(error){
    alert('請先登入');
    router.push('/login');
  }
}

const getTodoList = async ()=>{
  try {
    const res = await axios.get('https://todolist-api.hexschool.io/todos',{
      headers:{
        Authorization:token
      }
    });
    todoListData.value = res.data.data;
    completedTodos.value = todoListData.value.filter(todo=>todo.status).length;
  } catch (error) {
    alert('取得代辦事項失敗，請稍後再試');
  }
}

const addTodo = async ()=>{
  try {
    const res = await axios.post('https://todolist-api.hexschool.io/todos',todo.value,{
      headers:{
        Authorization:token
      },
    })
    console.log(res);
    getTodoList();
    todo.value.content = '';
  } catch (error) {
    console.log(error)
  }
}

const deleteTodo = async (id)=>{
  try{
    await axios.delete(`https://todolist-api.hexschool.io/todos/${id}`,{
      headers:{
        Authorization:token
      }
    })
    alert('刪除成功');
    getTodoList();
  }catch(error){
    alert('刪除失敗，請稍後再試');
  }
}

const toggleTodo = async (id,status)=>{
  try{
    await axios.patch(`https://todolist-api.hexschool.io/todos/${id}/toggle`,{status:!status},{
      headers:{
        Authorization:token
      }
    })
    getTodoList();
  }catch(error){
    console.log(error)
    alert('切換狀態失敗，請稍後再試');
  }
}

const setFilterStatus = (status)=>{
  filterStatus.value = status;
}

const filterTodos = computed(()=>{
  if(filterStatus.value === 'all'){
    return todoListData.value;
  }else if(filterStatus.value === 'completed'){
    return todoListData.value.filter(todo=>todo.status);
  }else{
    return todoListData.value.filter(todo=>!todo.status);
  }
})

const logout = async ()=>{
  try {
     await axios.post('https://todolist-api.hexschool.io/users/sign_out',{},{
      headers:{
        Authorization:token
      }
    })
    alert('登出成功');
    document.cookie = "hexToken=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/";
    router.push('/login');
  } catch (error) {
    alert('登出失敗，請稍後再試');
    console.log(error)
  }
}



onMounted(()=>{
  loginCheck();
  getTodoList();
})

</script>

<template>
  <div class="bg-half">
    <nav>
    <h1><a href="#">ONLINE TODO LIST</a></h1>
    <ul>
      <li class="todo_sm"><a href="#" @click.prevent="setFilterStatus('all')"><span>{{nickname}}的代辦</span></a></li>
      <li><a @click.prevent="logout" href="#">登出</a></li>
    </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="todo.content">
          <a href="#" class="d-flex align-items-center" @click.prevent="addTodo">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <div class="todoList_list">
          <ul class="todoList_tab">
            <li><a href="#" @click.prevent="setFilterStatus('all')" :class="{'active': filterStatus === 'all'}">全部</a></li>
            <li><a href="#" @click.prevent="setFilterStatus('incomplete')" :class="{'active': filterStatus === 'incomplete'}">待完成</a></li>
            <li><a href="#" @click.prevent="setFilterStatus('completed')" :class="{'active': filterStatus === 'completed'}">已完成</a></li>
          </ul>
          <p v-if="todoListData.length === 0" class="text-center">目前尚無待辦事項</p>
          <div v-else class="todoList_items">
            <ul class="todoList_item">
              <li v-for="todo in filterTodos" :key="todo.id">
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" @click="toggleTodo(todo.id,todo.status)" :checked="todo.status">
                  <span>{{todo.content}}</span>
                </label>
                <a href="#" @click.prevent="deleteTodo(todo.id)">
                  <i class="fa fa-times"></i>
                </a>
              </li>
            </ul>
            <div class="todoList_statistics">
              <p> {{completedTodos}} 個已完成項目</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

