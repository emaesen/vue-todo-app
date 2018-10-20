<template>
  <div class="ui basic content center aligned segment">
    <button
      v-show="!isCreating"
      class="ui basic button icon"
      @click="openTodoForm"
    >
      <i class="plus icon"/> Add a task
    </button>
    <div
      v-show="isCreating"
      class="ui centered card"
    >
      <div class="content">
        <div class="ui form">
          <div class="field">
            <label>Title</label>
            <input
              v-model="title"
              type="text"
            >
          </div>
          <div class="field">
            <label>Project Description</label>
            <input
              v-model="project"
              type="text"
            >
          </div>
          <div class="field">
            <label>Note / Comment</label>
            <input
              v-model="note"
              type="text"
            >
          </div>
          <div class="field">
            <label>Date Due</label>
            <input
              v-model="dateDue"
              type="date"
            >
          </div>
          <div class="ui two button attached buttons">
            <button
              class="ui basic blue button"
              @click="createTodo()"
            >
              Create
            </button>
            <button
              class="ui basic red button"
              @click="cancelTodo"
            >
              Cancel
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '',
      project: '',
      note: '',
      dateDue: '',
      isCreating: false,
    };
  },
  methods: {
    openTodoForm() {
      this.isCreating = true;
    },
    cancelTodo() {
      this.isCreating = false;
      this.clearTodoForm();
    },
    clearTodoForm() {
      this.title = '';
      this.project = '';
      this.note = '';
      this.dateDue = '';
      this.isCreating = false;
    },
    createTodo() {
      if (this.title.length > 0 && this.project.length > 0) {
        this.$emit('create-todo', {
          dateCreated: Date.now(),
          title: this.title,
          project: this.project,
          note: this.note,
          dateDue: this.dateDue? new Date(this.dateDue + "T00:00:00") : '',
          done: false,
        });
        this.clearTodoForm();
      } else {
        let txt = (this.title.length === 0)? "Please provide a title." : "";
        txt += (this.title.length === 0 && this.project.length === 0)? "\n" : "";
        txt += this.project.length === 0? "Please provide a project description." : "";
        this.$emit('create-todo-warning', {
          title: 'Missing input',
          text: txt
        });
      }
    },
  },
};
</script>
