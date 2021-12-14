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
      <li class="list-item card" :class="{isDone: task.checked}" v-for="(task, i) in taskList" :key="i">
        <input :id="i" type="checkbox" @click="task.checked = !task.checked">
        <label :for="i">{{ i + 1 }}. {{ task.text }}</label>
        <button class="btn danger" @click="removeTask(i)">Удалить</button>
      </li>
    </ul>
    <ul v-else class="card">
      Задач пока нет...
    </ul>
  </div>
</template>

<script>
import './theme.css'

export default {
  data() {
    return {
      task: '',
      taskList: [],
    }
  },
  methods: {
    createToDo() {
      if (this.task.length !== 0) {
        this.taskList.push(
            {
              text: this.task,
              checked: false
            }
        )
        this.task = ''
      }
    },
    removeTask(idx) {
      this.taskList.splice(idx, 1)
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