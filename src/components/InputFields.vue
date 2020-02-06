<template>
  <div class="input-fields-container">
      <i
        class="fas fa-plus input"
        @click="toggleState($event.target.classList[2].toUpperCase())"
      >
      </i>
      <div class="input-container">
        <transition name="input-fields">
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
      </div>
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
        console.log(this.state)
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

  // border: 1px solid red;

  .input-container {
    width: calc(600px - 48px);
    display: flex;
    flex-direction: column;
    height: 2rem;

    .todo-input {
      align-self: flex-start;
      text-align: left;
    }

    .todo-search {
      align-self: flex-end;
      text-align: right;
    }

    .todo-input, .todo-search {
      border: 1px solid lightgrey;
      width: calc(600px - 48px);
      max-width: calc(600px - 48px);
      height: 2rem;
      padding: 5px;
      font-size: 10px;
      position: absolute;
      // border: none;
      // margin: 0 5px;

      &:focus {
        outline: 0;
      }
    }

    .input-fields-enter-active {
      width: 100%;
      transition: width 1s ease;
    }

    .input-fields-leave-active
    {
      width: 0;
      transition: width 1s ease;
    }

    .input-fields-enter {
      width: 0;
      max-width: 0;
    }
    .input-fields-leave-to
    {
      width: 0;
    }
  }

  .fas {
    &:hover {
      cursor: pointer;
    }
  }
}

</style>
