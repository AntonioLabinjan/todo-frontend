<template>
    <div>
      <h1>Vue 3 ToDo App</h1>
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Add a task" />
      <ul>
        <li v-for="todo in todos" :key="todo.id" :class="{ completed: todo.completed }">
          <span @dblclick="editTodo(todo)">{{ todo.task }}</span>
          <div>
            <button class="toggle" @click="toggleDone(todo)">✓</button>
            <button class="edit" @click="editTodo(todo)">Edit</button>
            <button class="delete" @click="deleteTask(todo.id)">Delete</button>
          </div>
          <input v-if="todo === editingTodo" v-model="todo.task" @keyup.enter="updateTask(todo)" />
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        todos: [],
        newTask: '',
        editingTodo: null
      }
    },
    methods: {
      async fetchTodos() {
        const response = await this.$axios.get('http://localhost:3000/todos');
        this.todos = response.data.data;
      },
      async addTask() {
        if (this.newTask === '') return;
        const response = await this.$axios.post('http://localhost:3000/todos', {
          task: this.newTask,
          completed: false
        });
        this.todos.push({ id: response.data.id, task: this.newTask, completed: false });
        this.newTask = '';
      },
      async updateTask(todo) {
        await this.$axios.put(`http://localhost:3000/todos/${todo.id}`, {
          task: todo.task,
          completed: todo.completed
        });
        this.editingTodo = null;
      },
      async deleteTask(id) {
        await this.$axios.delete(`http://localhost:3000/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
      },
      async toggleDone(todo) {
        todo.completed = !todo.completed;
        await this.$axios.put(`http://localhost:3000/todos/${todo.id}`, {
          task: todo.task,
          completed: todo.completed
        });
      },
      editTodo(todo) {
        this.editingTodo = todo;
      },
      /*doneEditing(todo) {
        if (this.editingTodo) {
          this.updateTask(this.editingTodo);
          this.editingTodo = null;
        }
      }*/
    },
    mounted() {
      this.fetchTodos();
    }
  }
  </script>
  