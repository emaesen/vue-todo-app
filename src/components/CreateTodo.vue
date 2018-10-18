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
              v-model="titleText"
              type="text"
            >
          </div>
          <div class="field">
            <label>Project</label>
            <input
              v-model="projectText"
              type="text"
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
      titleText: '',
      projectText: '',
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
      this.titleText = '';
      this.projectText = '';
      this.isCreating = false;
    },
    createTodo() {
      if (this.titleText.length > 0 && this.projectText.length > 0) {
        const title = this.titleText;
        const project = this.projectText;
        this.$emit('create-todo', {
          dateCreated: Date.now(),
          title,
          project,
          done: false,
        });
        this.clearTodoForm();
      } else {
        let txt = (this.titleText.length === 0)? "Please provide a title." : "";
        txt += (this.titleText.length === 0 && this.projectText.length === 0)? "\n" : "";
        txt += this.projectText.length === 0? "Please provide a project description." : "";
        this.$emit('create-todo-warning', {
          title: 'Missing input',
          text: txt
        });
      }
    },
  },
};
</script>
