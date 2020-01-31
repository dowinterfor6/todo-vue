<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="What needs to be done"
      v-model="newTodo"
      @keyup.enter="willAddTodo"
    >
  </div>
</template>

<script>
import EventBus from '../eventBus/event-bus'

export default {
  name: 'input-fields',
  data () {
    return {
      newTodo: ''
    }
  },
  methods: {
    willAddTodo () {
      EventBus.$emit('addTodo', this.newTodo)
      this.newTodo = ''
    }
  }
}
</script>

<style scoped lang="scss">
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 10px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }
</style>
