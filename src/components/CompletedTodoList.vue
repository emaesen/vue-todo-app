<template>
  <div>
    <p class="tasks">Completed Tasks: {{ todos.length }}</p>
    <todo-item
      v-for="(todo, index) in todos"
      :todo.sync="todo"
      :index.sync="index"
      :key="todo.dateDeleted"
      @delete-todo="deleteTodo"
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
        return []
      }
    }
  },
  methods: {
    deleteTodo(todo) {
      this.$emit('delete-todo', todo);
    }
  }
};
</script>

<style scoped>
p.tasks {
  text-align: center;
}
</style>
