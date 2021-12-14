<template>
  <div class="container">
    <div class="card">
      <h1>TODO list</h1>
      <form class="form-control" @submit.prevent="createToDo">
        <input placeholder="Write a task..." v-model="task" type="text">
        <button :disabled="!task.length" class="btn">Create todo</button>
      </form>
    </div>
    <ul v-if="taskList.length" class="card">
      <li class="list-item card" :class="{isDone: task.checked}" v-for="(task, i) in taskList" :key="task.id">
        <input :id="task.id" type="checkbox" @click="checkedTask(task.id, task.checked, task.text)">
        <label :for="task.id">{{ i + 1 }}. {{ task.text }}</label>
        <button class="btn danger" @click="removeTask(task.id)">Удалить</button>
      </li>
    </ul>
    <ul v-else class="card">
      Задач пока нет...
    </ul>
  </div>
</template>

<script>
import './theme.css'
import axios from 'axios'

export default {
  data() {
    return {
      task: '',
      taskList: [],
      id: null,
    }
  },
  mounted() {
    this.loadToDo()
  },
  methods: {
    async loadToDo() {
      const {data} = await axios.get('https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo.json');
      this.taskList = Object.keys(data).map(key => {
        return {
          ...data[key],
          id: key,
        }
      })
    },
    async createToDo() {
      try {
        const {data} = await axios.post('https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo.json', {
          text: this.task,
          checked: false,
        })
        const response = await axios.get(`https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo/${data.name}.json`)
        this.taskList.push({...response.data, id: data.name})
        this.task = '';
      } catch (e) {
        console.log(e.message)
      }
    },
    async removeTask(id) {
      try {
        await axios.delete(`https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo/${id}.json`)
        this.taskList = this.taskList.filter(task => task.id !== id)
      } catch (e) {
        console.log(e.message)
      }
    },
    async checkedTask(id, done, text) {
      console.log(done)
      const {data} = await axios.put(`https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo/${id}.json`, {
        text: text,
        checked: !done,
        id: id
      })
      const task = this.taskList.find(el => el.id === id)
      task.checked = data.checked
      console.log(task)
      console.log(data)
    }
  }
}
</script>

<style>
.isDone {
  background-color: #3eaf7c;
  color: #eeeeee;
  text-decoration: line-through;
}

</style>