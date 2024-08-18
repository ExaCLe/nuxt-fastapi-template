<template>
  <div>
    <h1>Todo List</h1>

    <!-- Form to Add Todo -->
    <form @submit.prevent="addTodo">
      <input v-model="newTodo" placeholder="Enter todo" required />
      <button type="submit">Add Todo</button>
    </form>

    <!-- List of Todos -->
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        {{ todo.name }} (Created: {{ new Date(todo.created).toLocaleString() }})
        <button @click="deleteTodo(todo.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodo: '',      // For the input field
      todos: [],        // List of todos
    }
  },
  methods: {
    async fetchTodos() {
      try {
        const response = await this.$axios.get('/todos/');
        this.todos = response.data;
      } catch (error) {
        console.error('Error fetching todos:', error);
      }
    },
    async addTodo() {
      if (this.newTodo.trim() === '') return;

      try {
        const response = await this.$axios.post('/todos/', { name: this.newTodo });
        this.todos.push(response.data);
        this.newTodo = '';  // Clear the input field
      } catch (error) {
        console.error('Error adding todo:', error);
      }
    },
    async deleteTodo(id) {
      try {
        await this.$axios.delete(`/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    }
  },
  async mounted() {
    await this.fetchTodos();  // Fetch todos when the component is mounted
  }
}
</script>

<style scoped>
/* Add your styles here */
</style>
