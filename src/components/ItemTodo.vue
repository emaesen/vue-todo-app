<template>
  <div
    :id="'todo-'+todo.dateCreated"
    :class="dueClass"
    class="ui centered card"
  >
    <div
      v-show="!isEditing"
      class="content"
    >
      <div
        v-show="todo.dateDue"
        class="right aligned"
      >
        Due: {{ formattedDueDate }}
      </div>
      <div class="header">
        <span class="meta">{{ index+1 }}.</span> {{ todo.title }}
      </div>
      <div class="content">
        {{ todo.project }}
      </div>
      <div class="meta">
        {{ todo.note }}
      </div>
      <div class="right floated extra content ui mini basic icon buttons">
        <span
          v-show="!isCompleted"
          class="edit ui primary basic icon button"
          title="edit"
          @click="showForm()"
        >
          <i class="edit icon"/> edit
        </span>
        <span
          v-show="!isCompleted"
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
      Start this task &nbsp; <i class="play icon"/>
    </div>
    <div
      v-show="isInProgress"
      class="ui bottom attached blue basic button"
      @click="completeTodo(todo)"
    >
      Complete this task &nbsp; <i class="stop icon"/>
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

  const DAY = 1000 * 60 * 60 * 24;

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
            dateDue:"",
            dateCompleted:"",
            title:"",
            project:"",
            note:"",
            status:""
          }
        }
      }
    },
    data() {
      return {
        isEditing: false,
        STATUS: app.constants.STATUS
      };
    },
    computed: {
      formattedDueDate: function() {
        let dateFormat = {
          //weekday:'short',
          month:'short',
          day:'numeric',
          //year:'numeric'
        }
        return this.todo.dateDue && this.todo.dateDue.toLocaleDateString('en-US', dateFormat);
      },
      isPastDue: function() {
        return this.todo.dateDue && (this.todo.dateDue.getTime()) < (new Date().getTime());
      },
      isDueSoon: function() {
        const window = DAY * 2;
        return this.todo.dateDue && ((this.todo.dateDue.getTime()) - (new Date().getTime())) < window;
      },
      isOpen: function() {
        return !this.isEditing && this.todo.status === this.STATUS.OPEN
      },
      isInProgress: function() {
        return !this.isEditing && this.todo.status === this.STATUS.PROGRESS
      },
      isCompleted: function() {
        return !this.isEditing && this.todo.status === this.STATUS.COMPLETE
      },
      dueClass: function() {
        return this.isCompleted? 'complete'
          : this.isPastDue? 'pastdue'
          : this.isDueSoon? 'duesoon'
          : 'notyetdue'
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
        this.origTodo = Object.assign({}, this.todo);
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
        this.todo.note = this.origTodo.note;
        this.todo.dateDue = this.origTodo.dateDue;
        this.isEditing = false;
      }
    }
  };
</script>

<style>
.notyetdue{
  background-color: #2bff0410!important;
}
.duesoon{
  background-color: #ff6a0020!important;
}
.pastdue{
  background-color: #ff000010!important;
}
.complete{
  background-color: #415ca710!important;
}
</style>
