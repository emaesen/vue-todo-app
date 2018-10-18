<template>
  <div id="app">
    <h1 class="ui dividing centered header">
      Vue.js Todo App
    </h1>
    <create-todo
      @create-todo="createTodo"
      @create-todo-warning="createTodoWarning"
    />
    <div class="ui two column centered grid">
      <div class="column">
        <todo-list
          :todos="openTodos"
          @delete-todo="deleteTodo"
          @complete-todo="completeTodo"
          @edit-todo="editTodo"
        />
      </div>
      <div class="column">
        <completed-todo-list
          :todos="completedTodos"
          @delete-todo="deleteTodo"
        />
      </div>
    </div>
    <create-dummy-todos
      @create-todo="createTodo"
    />
  </div>
</template>

<script>
import swalModal from 'sweetalert';
import TodoList from './components/TodoList';
import CompletedTodoList from './components/CompletedTodoList';
import CreateTodo from './components/CreateTodo';
import CreateDummyTodos from './components/CreateDummyTodos';

export default {
  name: 'App',
  components: {
    TodoList,
    CompletedTodoList,
    CreateTodo,
    CreateDummyTodos
  },
  data() {
    return {
      todos: []
    };
  },
  computed: {
    openTodos: function() {
      return this.todos
        .filter(todo => {return todo.done === false});
    },
    completedTodos: function() {
      return this.todos
        .filter(todo => {return todo.done === true})
        .sort((a,b) => {return b.dateCompleted - a.dateCompleted});
    }
  },
  methods: {
    createTodo(newTodo) {
      this.todos.push(newTodo);
      swalModal('Success!', 'To-Do ' + newTodo.title + ' has been created', 'success');
    },
    deleteTodo(todo) {
      swalModal({
        title: 'Are you sure?',
        text: 'To-Do "' + todo.title + '" will be permanently deleted!',
        icon: 'warning',
        buttons: true
      })
      .then((doDelete) => {
        if (doDelete) {
          const todoIndex = this.todos.indexOf(todo);
          this.todos.splice(todoIndex, 1);
          swalModal('Deleted!', 'Your To-Do "' + todo.title + '" has been deleted', 'success');
        } else {
          //swalModal('Your To-Do has been restored.')
        }
      });
    },
    completeTodo(todo) {
      swalModal('Task Complete?', 'To-Do "' + todo.title + '"  will be marked as completed', 'info', {
        buttons: {cancel:true, ok:true}
      })
      .then((action) => {

        if (action === "ok") {
          todo.dateCompleted = Date.now();
          todo.done = true;
          swalModal('Success!', 'To-Do "' + todo.title + '"  has been completed', 'success')
        }
      });
    },
    editTodo(todo) {
      swalModal('Success!', 'To-Do "' + todo.title + '"  has been edited', 'success')
    },
    createTodoWarning(warning) {
      swalModal(warning.title, warning.text, 'warning');
    }
  }
};
</script>

<style>
.swal-modal {
  animation: unset!important;
}
</style>
