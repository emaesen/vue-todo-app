<template>
  <div>
    <p class="tasks">Open Tasks: {{ todos.length }}</p>
    <todo-item
      v-for="(todo, index) in todos"
      :todo.sync="todo"
      :index.sync="index"
      :key="todo.dateCreated"
      @delete-todo="deleteTodo"
      @complete-todo="completeTodo"
      @edit-todo="editTodo"
    />
  </div>
</template>

<script type = "text/javascript" >
import TodoItem from './TodoItem';

export default {
  components: {
    TodoItem,
  },
  props: {
    todos: {
      type: Array,
      default: function() {
        return [];
      }
    }
  },
  methods: {
    completeTodo(todo) {
      this.$emit('complete-todo', todo);
    },
    deleteTodo(todo) {
      this.$emit('delete-todo', todo);
    },
    editTodo(todo) {
      this.$emit('edit-todo', todo);
    }
  }
};
</script>

<style scoped>
p.tasks {
  text-align: center;
}
</style>
