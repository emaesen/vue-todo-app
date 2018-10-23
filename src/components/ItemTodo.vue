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
        v-if="dueDateText && !isCompleted"
        class="right aligned"
      >
        Due: {{ dueDateText }}
      </div>
      <div
        v-else>
        &nbsp;
      </div>
      <div class="header">
        <span class="meta">{{ index+1 }}.</span> {{ todo.title }}
      </div>
      <div>
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
    <edit-todo
      v-show="isEditing"
      :todo="todo"
      @edit-todo="editTodo"
      @cancel-edit="cancelEdit"
      @edit-todo-warning="editTodoWarning"
    />
  </div>
</template>

<script type="text/javascript">
  import app from '../App';
  import EditTodo from './CreateEditTodo';

  const DAY = 1000 * 60 * 60 * 24;

  export default {
    components: {
      EditTodo
    },
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
      dueDateObj: function() {
        return this.todo.dateDue? new Date(this.todo.dateDue + "T00:00:00") : null;
      },
      dueDateText: function() {
        let dateText = "";
        if (this.dueDateObj && !Number.isNaN(this.dueDateObj.getTime())){
          let dateFormat = {
            //weekday:'short',
            month:'short',
            day:'numeric',
            //year:'numeric'
          }
          let window = 7;
          let dueInNrDays = Math.ceil((this.dueDateObj.getTime() - new Date().getTime()) / DAY);
          if (dueInNrDays < window) {
            dateText = dueInNrDays===0? "today!"
            : dueInNrDays===1? "tomorrow"
            : dueInNrDays===-1? "yesterday!!"
            : dueInNrDays<-1? -dueInNrDays + " days ago!!!"
            : "in " + dueInNrDays + " days";
          } else {
            dateText = this.dueDateObj.toLocaleDateString('en-US', dateFormat);
          }
        }
        return dateText;
      },
      isPastDue: function() {
        return this.dueDateObj && (this.dueDateObj.setHours(0,0,0,0)) < (new Date().setHours(0,0,0,0));
      },
      isDueToday: function() {
        const window = DAY;
        return this.dueDateObj && ((this.dueDateObj.setHours(0,0,0,0)) - (new Date().setHours(0,0,0,0))) < window;
      },
      isDueSoon: function() {
        const window = DAY * 3;
        return this.dueDateObj && ((this.dueDateObj.setHours(0,0,0,0)) - (new Date().setHours(0,0,0,0))) < window;
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
          : this.isDueToday? 'duetoday'
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
      editTodo(mod) {
        this.isEditing = false;
        this.$emit('edit-todo', {todo:this.todo, mod:mod});
      },
      cancelEdit() {
        this.todo.title = this.origTodo.title;
        this.todo.project = this.origTodo.project;
        this.todo.note = this.origTodo.note;
        this.todo.dateDue = this.origTodo.dateDue;
        this.isEditing = false;
      },
      editTodoWarning(warning) {
        this.$emit('edit-todo-warning', warning);
      }
    }
  };
</script>

<style>
.card{
  box-shadow: 4px 4px 2px 0 #D4D4D5, 0 0 0 1px #D4D4D5!important;
}
.content{
  padding:.4em .7em!important;
}
.meta{
  margin-top: .4em;
  font-size:.75em!important;
}
.notyetdue{
  background-color: #2bff0410!important;
}
.duesoon{
  background-color: #ff6a0020!important;
}
.duetoday{
  background-color: #ff000030!important;
}
.pastdue{
  background-color: #ff000050!important;
}
.complete{
  background-color: #415ca710!important;
}
</style>
