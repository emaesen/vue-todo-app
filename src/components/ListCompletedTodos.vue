<template>
  <div>
    <p class="tasks">Completed Tasks: {{ todos.length }}</p>
    <item-todo
      v-for="(todo, index) in todos"
      :todo.sync="todo"
      :index.sync="index"
      :key="todo.dateCreated"
      @delete-todo="deleteTodo"
    />
  </div>
</template>

<script type = "text/javascript" >
import ItemTodo from './ItemTodo';

export default {
  components: {
    ItemTodo,
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
