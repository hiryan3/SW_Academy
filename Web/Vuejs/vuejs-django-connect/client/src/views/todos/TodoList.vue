<template>
  <div>
    <input type="text" v-model="userInput">
    <button @click="createTodo">+</button>
    <ul>
      <li v-for="(todo, idx) in todos" :key="idx">
        <span @click="updateTodoStatus(todo)" :class="{ completed: todo.completed }">{{ todo.title }}</span>
        <button @click="deleteTodo(todo)" class="todo-btn">X</button>
      </li>
    </ul>
    <button @click="getTodos">Get Todos</button>
  </div>
</template>

<script>

import axios from 'axios'

const SERVER_URL = process.env.VUE_APP_SERVER_URL

export default {
  name: 'TodoList',
  data: function () {
    return {
      todos: null,
      userInput: '',
    }
  },
  methods: {
    getTodos: function () {
      axios({
        method: 'get',
        url: `${SERVER_URL}/todos/`
      })
        .then((res) => {
          console.log(res)
          this.todos = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    deleteTodo: function (todo) {
      axios({
        method: 'delete',
        url: `http://127.0.0.1:8000/todos/${todo.id}/`
      })
        .then((res) => {
          console.log(res.data)
          // 전체데이터 다시 불러오기
          // this.getTodos()

          // id가 다른것만 새로운 배열로 만들기
          this.todos = this.todos.filter((t)=>{
            return t.id !== todo.id
          })

        })
        .catch((err) => {
          console.log(err)
        })
    },
    updateTodoStatus: function (todo) {
      const todoItem = {
        ...todo,
        completed: !todo.completed
      }

      axios({
        method: 'put',
        url: `http://127.0.0.1:8000/todos/${todo.id}/`,
        data: todoItem,
      })
        .then((res) => {
          console.log(res)
          todo.completed = res.data.completed
        })
    },
    createTodo: function () {
      const todoItem = {
        title: this.userInput,
      }

      if (todoItem.title) {
        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/todos/',
          data: todoItem
        })
          .then((res) => {
            console.log(res.data)
            // this.$router.push({ name: 'TodoList' })
            // this.getTodos()
            this.todos.push(res.data)
            
          })
          .catch((err) => {
            console.log(err)
          })
        }
    },

  
  },
  created: function () {
    this.getTodos()
  }
}
</script>

<style scoped>
  .todo-btn {
    margin-left: 10px;
  }

  .completed {
    text-decoration: line-through;
    color: rgb(112, 112, 112);
  }
</style>
