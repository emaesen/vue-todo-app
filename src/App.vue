<template>
  <div id="app">
    <h1 class="ui dividing centered header">
      Vue.js Todo App
    </h1>
    <create-todo
      @create-todo="createTodo"
      @create-todo-warning="createTodoWarning"
    />
    <div class="ui three column centered grid">
      <div class="column">
        <list-open-todos
          :todos="openTodos"
          @delete-todo="deleteTodo"
          @progress-todo="progressTodo"
          @edit-todo="editTodo"
        />
      </div>
      <div class="column">
        <list-in-progress-todos
          :todos="inProgressTodos"
          @delete-todo="deleteTodo"
          @complete-todo="completeTodo"
          @edit-todo="editTodo"
        />
      </div>
      <div class="column">
        <list-completed-todos
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
import ListOpenTodos from './components/ListOpenTodos';
import ListInProgressTodos from './components/ListInProgressTodos';
import ListCompletedTodos from './components/ListCompletedTodos';
import CreateTodo from './components/CreateTodo';
import CreateDummyTodos from './components/CreateDummyTodos';
const STATUS = {
  OPEN: "open",
  PROGRESS: "progress",
  COMPLETE: "complete"
};

export default {
  name: 'App',
  components: {
    ListOpenTodos,
    ListInProgressTodos,
    ListCompletedTodos,
    CreateTodo,
    CreateDummyTodos
  },
  constants: {
    STATUS
  },
  data() {
    return {
      todos: []
    };
  },
  computed: {
    openTodos: function() {
      // order by due date (earliest due on top)
      return this.todos
        .filter(todo => {return todo.status === STATUS.OPEN})
        .sort((a,b) => {return !a.dateDue? 1 : a.dateDue - b.dateDue});
    },
    inProgressTodos: function() {
      // order by due date (earliest due on top)
      return this.todos
        .filter(todo => {return todo.status === STATUS.PROGRESS})
        .sort((a,b) => {return !a.dateDue? 1 : a.dateDue - b.dateDue});
    },
    completedTodos: function() {
      // order by date completed (latest on top)
      return this.todos
        .filter(todo => {return todo.status === STATUS.COMPLETE})
        .sort((a,b) => {return b.dateCompleted - a.dateCompleted});
    }
  },
  methods: {
    createTodo(todo) {
      todo.status = STATUS.OPEN;
      this.todos.push(todo);
      swalModal('Success!', 'To-Do ' + todo.title + ' has been created', 'success');
    },
    progressTodo(todo) {
      todo.status = STATUS.PROGRESS;
      swalModal('Success!', 'To-Do ' + todo.title + ' is now in progress', 'success');
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
          todo.status = STATUS.COMPLETE;
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
