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
import CreateTodo from './components/CreateEditTodo';
import CreateDummyTodos from './components/CreateDummyTodos';
import storage from './storage.js';
const STATUS = {
  OPEN: "open",
  PROGRESS: "progress",
  COMPLETE: "complete"
};
const STORAGEKEY = "todos";
const STORAGETYPE = "local"


// although this config is a constant within the current implementation, it's defined as a variable so it can be used as such later.
let config = {
  showSuccessModal: false,
  showConfirmModal: true
};

function dateObj(dateStr) {
  // convert yyyy-MM-dd string to date object
  return dateStr? new Date(dateStr + "T00:00:00") : null;
}
function sortByDateDue(a,b) {
  return !a.dateDue? 1
    : !b.dateDue? -1
    : dateObj(a.dateDue) - dateObj(b.dateDue);
}
function sortByDateCreated(a,b) {
  return b.dateCreated - a.dateCreated;
}
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
      todos: storage.getItem(STORAGEKEY, STORAGETYPE) || []
    };
  },
  computed: {
    openTodos: function() {
      // order by due date (earliest due on top)
      return this.todos
        .filter(todo => {return todo.status === STATUS.OPEN})
        .sort((a,b) => {return sortByDateCreated(a,b)})
        .sort((a,b) => {return sortByDateDue(a,b)});
    },
    inProgressTodos: function() {
      // order by due date (earliest due on top)
      return this.todos
        .filter(todo => {return todo.status === STATUS.PROGRESS})
        .sort((a,b) => {return sortByDateCreated(a,b)})
        .sort((a,b) => {return sortByDateDue(a,b)});
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
      if (config.showSuccessModal) {
        swalModal('Success!', 'To-Do ' + todo.title + ' has been created', 'success');
      }
      storage.setItem(STORAGEKEY, this.todos, STORAGETYPE);
    },
    progressTodo(todo) {
      todo.status = STATUS.PROGRESS;
      if (config.showSuccessModal) {
        swalModal('Success!', 'To-Do ' + todo.title + ' is now in progress', 'success');
      }
      storage.setItem(STORAGEKEY, this.todos, STORAGETYPE);
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
          if (config.showSuccessModal) {
            swalModal('Deleted!', 'Your To-Do "' + todo.title + '" has been deleted', 'success');
          }
          storage.setItem(STORAGEKEY, this.todos, STORAGETYPE);
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
          if (config.showSuccessModal) {
            swalModal('Success!', 'To-Do "' + todo.title + '"  has been completed', 'success')
          }
          storage.setItem(STORAGEKEY, this.todos, STORAGETYPE);
        }
      });
    },
    editTodo(obj) {
      let todo = obj.todo;
      const mod = obj.mod;
      todo.title = mod.title;
      todo.project = mod.project;
      todo.note = mod.note;
      todo.dateDue = mod.dateDue;
      if (config.showSuccessModal) {
        swalModal('Success!', 'To-Do "' + todo.title + '"  has been edited', 'success')
      }
      storage.setItem(STORAGEKEY, this.todos, STORAGETYPE);
    },
    createTodoWarning(warning) {
      swalModal(warning.title, warning.text, 'warning');
    },
    editTodoWarning(warning) {
      swalModal(warning.title, warning.text, 'warning');
    }
  }
};
</script>

<style>
#app{
  padding:1em;
}
body{
  font-size:13px;
}
h2.tasks {
  text-align: center;
  font-size:1.5rem;
}
i.icon.caret{
  margin: 0;
}
.ui.form input,
.ui.form textarea{
  padding: .3em!important;
}
.swal-modal {
  animation: unset!important;
}
._titlewrapper{
  margin:0 -.6em;
}
</style>
