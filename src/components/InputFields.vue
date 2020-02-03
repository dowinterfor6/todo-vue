<template>
  <div class="input-fields-container">
      <i
        class="fas fa-plus input"
        @click="toggleState($event.target.classList[2].toUpperCase())"
      >
      </i>
      <transition name="input-fields" mode="out-in">
        <input
          type="text"
          class="todo-input"
          key="INPUT"
          placeholder="What needs to be done"
          v-model="newTodo"
          @keyup.enter="willAddTodo"
          v-if="state === 'INPUT'"
        >
        <input
          type="text"
          class="todo-search"
          key="SEARCH"
          placeholder="Look for a todo"
          v-model="searchTerm"
          @keyup.enter="willAddTodo"
          v-if="state === 'SEARCH'"
        >
      </transition>
      <i
        class="fas fa-search search"
        @click="toggleState($event.target.classList[2].toUpperCase())"
      >
      </i>
  </div>
</template>

<script>
import EventBus from '../eventBus/event-bus'

export default {
  name: 'input-fields',
  data () {
    return {
      newTodo: '',
      searchTerm: '',
      state: 'INPUT',
      searchComponent: document.getElementById('search-container'),
      inputComponent: document.getElementById('input-container')
    }
  },
  methods: {
    willAddTodo () {
      EventBus.$emit('addTodo', this.newTodo)
      this.newTodo = ''
    },
    toggleState (state) {
      if (state !== this.state) {
        this.state = state
      }
    }
  }
}
</script>

<style scoped lang="scss">

.input-fields-container {
  display: flex;
  justify-content: space-between;
  align-items: center;

  border: 1px solid red;

  .todo-input, .todo-search {
    border: 1px solid green;
    width: 100%;
    padding: 10px;
    font-size: 10px;
    // border: none;
    margin: 0 5px;

    &:focus {
      outline: 0;
    }
  }

  .input-fields-enter-active,
  .input-fields-leave-active
  {
    transition: opacity 0.25s ease;
  }

  .input-fields-enter,
  .input-fields-leave-to
  {
    opacity: 0;
  }

  .fas {
    &:hover {
      cursor: pointer;
    }
  }
}

</style>
