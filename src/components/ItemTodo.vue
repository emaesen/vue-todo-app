<template>
  <div
    :id="'todo-'+todo.dateCreated"
    class="ui centered card"
  >
    <div
      v-show="!isEditing"
      class="content"
    >
      <div class="header">
        <span class="meta">{{ index+1 }}.</span> {{ todo.title }}
      </div>
      <div class="content">
        {{ todo.project }}
      </div>
      <div class="right floated extra content ui mini basic icon buttons">
        <span
          v-show="!todo.done"
          class="edit ui primary basic icon button"
          title="edit"
          @click="showForm()"
        >
          <i class="edit icon"/> edit
        </span>
        <span
          v-show="!todo.done"
          class="or"
        />
        <span
          class="trash ui negative basic icon button"
          title="delete"
          @click="deleteTodo(todo)"
        >
          <i class="trash outline icon"/> delete
        </span>
      </div>
    </div>
    <div
      v-show="isEditing"
      class="content"
    >
      <div class="ui form">
        <div class="field">
          <label>Title</label>
          <input
            v-model="todo.title"
            type="text"
          >
        </div>
        <div class="field">
          <label>Project</label>
          <input
            v-model="todo.project"
            type="text"
          >
        </div>
        <div class="ui two button attached buttons">
          <button
            class="ui basic blue button"
            @click="saveEdit()"
          >
            Save
          </button>
          <button
            class="ui basic red button"
            @click="cancelEdit()"
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
    <div
      v-show="isOpen"
      class="ui bottom attached blue basic button"
      @click="progressTodo(todo)"
    >
      Work on this task
    </div>
    <div
      v-show="isInProgress"
      class="ui bottom attached blue basic button"
      @click="completeTodo(todo)"
    >
      Complete this task
    </div>
    <div
      v-show="isCompleted"
      class="ui disabled bottom attached green basic button"
      disabled
    >
      Completed
    </div>
  </div>
</template>

<script type="text/javascript">
  import app from '../App';
  //const STATUS = app.constants.STATUS;

  export default {
    props: {
      index: {
        type: Number,
        default: 0
      },
      todo: {
        type: Object,
        default: function() {
          return {
            dateCreated:"",
            dateCompleted:"",
            dateDue:"",
            title:"",
            project:"",
            status:""
          }
        }
      }
    },
    data() {
      return {
        isEditing: false
      };
    },
    computed: {
      isOpen: function() {
        return !this.isEditing && this.todo.status === app.constants.STATUS.OPEN
      },
      isInProgress: function() {
        return !this.isEditing && this.todo.status === app.constants.STATUS.PROGRESS
      },
      isCompleted: function() {
        return !this.isEditing && this.todo.status === app.constants.STATUS.COMPLETE
      }
    },
    methods: {
      progressTodo(todo) {
        this.$emit('progress-todo', todo);
      },
      completeTodo(todo) {
        this.$emit('complete-todo', todo);
      },
      deleteTodo(todo) {
        this.$emit('delete-todo', todo);
      },
      showForm() {
        this.isEditing = true;
        this.origTodo = {
          title: this.todo.title,
          project: this.todo.project
        }
      },
      saveEdit() {
        this.isEditing = false;
        if (this.todo.title !== this.origTodo.title
            || this.todo.project !== this.origTodo.project) {
          this.$emit('edit-todo', this.todo);
        }
      },
      cancelEdit() {
        this.todo.title = this.origTodo.title;
        this.todo.project = this.origTodo.project;
        this.isEditing = false;
      }
    }
  };
</script>
