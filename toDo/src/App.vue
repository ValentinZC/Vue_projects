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
        <input :id="task.id" type="checkbox" @click="checkedTask(task.id)">
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
          id: key,
          ...data[key]
        }
      })
    },
    async createToDo() {
      await axios.post('https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo.json', {
        text: this.task,
        checked: false,
      })
      this.taskList.push(
                {
                  text: this.task,
                  checked: false,
                  id: this.task.id
                }
            )
      this.task = '';
    },
   async removeTask(id) {
      await axios.delete(`https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo/${id}.json`)
      this.taskList = this.taskList.filter(task => task.id !== id)
    },
    async checkedTask(id) {
       const task = this.taskList.find(task => task.id === id);
        task.checked = !this.checked
        await axios.put(`https://todo-list-valentinzc-default-rtdb.europe-west1.firebasedatabase.app/todo/${id}.json`, {
        checked: !task.checked
      })
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