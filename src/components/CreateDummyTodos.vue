<template>
  <div class="ui basic center aligned segment">
    <hr class="ui">
    <div class="ui">
      Create a few dummy Todos:<br>
      (for development purposes)
    </div>
    <div class="ui action input">
      <input
        v-model="nrOfDummyTodos"
        class="ui action input"
        placeholder="enter a number > 0"
        type="number"
      >
      <button
        class="ui button"
        @click="createDummyTodos()"
      >
        Go!
      </button>
    </div>
  </div>
</template>


<script>
export default {
  data() {
    return {
      nrOfDummyTodos: null,
      dummyTodoCounter: 0,
    };
  },
  methods: {
    createDummyTodos() {
      /*
      let that = this;
      async function addTodo() {
        let todos = that.todos;
        function delay() {
          return new Promise(x => {
            setTimeout(() => {x();}, 100);
          });
        }
        // add delay to ensure dateCreated is unique
        await delay();
        let cntr = ++that.dummyTodoCounter;
        todos.push({
          dateCreated: Date.now(),
          title: 'Todo ' + cntr,
          project: 'Project ' + cntr,
          done: false,
        });
      }
      */
      for (let i=0; i<this.nrOfDummyTodos; i++) {
        //addTodo();
        let cntr = ++this.dummyTodoCounter;
        this.$emit('create-todo', {
          dateCreated: Date.now(),
          title: 'Todo title ' + cntr,
          project: 'Short project description ' + cntr
        });
      }
    },
  }
}
</script>
