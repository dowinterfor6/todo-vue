<template>
  <div class="todo-list-wrapper">
    <input
      type="text"
      class="todo-input"
      placeholder="What needs to be done"
      v-model="newTodo"
      @keyup.enter="addTodo"
    >
    <search></search>
    <transition-group
      name="fade"
      enter-active-class="animated fadeInUp"
      leave-active-class="animated fadeOutDown"
    >
      <todo-item
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        :todo="todo"
        :index="index"
        :checkAll="!areAllIncomplete"
        @removedTodo="removeTodo"
        @finishedEdit="finishedEdit"
      >
      </todo-item>
    </transition-group>

    <div class="extra-container">
      <div>
        <label>
          <input
            type="checkbox"
            :checked="!areAllIncomplete"
            @change="checkAllTodos"
          >
          Check All
        </label>
      </div>
      <div>
        {{ incompleteTodosDisplay }}
      </div>
    </div>

    <div class="extra-container">
      <div>
        <button
          :class="{ active: filter === 'all' }"
          @click="filter = 'all'"
        >
          All
        </button>
        <button
          :class="{ active: filter === 'incomplete' }"
          @click="filter = 'incomplete'"
        >
          Incomplete
        </button>
        <button
          :class="{ active: filter === 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>

      <div>
        <transition name="fade">
          <button
            v-if="showClearCompletedButton"
            @click="clearCompleted"
          >
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import Search from './Search'
import TodoItem from './TodoItem'

export default {
  name: 'TodoList',
  components: {
    Search,
    TodoItem
  },
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      todos: [
        {
          'id': 1,
          'title': 'Finish Vue Screencast',
          'completed': true,
          'editing': false
        },
        {
          'id': 2,
          'title': 'Take over the world',
          'completed': false,
          'editing': false
        }
      ],
      filter: 'all'
    }
  },
  computed: {
    incompleteTodos () {
      return this.todos.filter(todo => !todo.completed).length
    },
    areAllIncomplete () {
      return this.incompleteTodos !== 0
    },
    todosFiltered () {
      switch (this.filter) {
        case 'all':
          return this.todos
        case 'incomplete':
          return this.todos.filter(todo => !todo.completed)
        case 'completed':
          return this.todos.filter(todo => todo.completed)
      }
    },
    showClearCompletedButton () {
      return this.todos.filter(todo => todo.completed).length > 0
    },
    incompleteTodosDisplay () {
      const incompleteTodos = this.incompleteTodos

      switch (incompleteTodos) {
        case 1:
          return incompleteTodos + ' item left'
        case 0:
          return 'All done!'
        default:
          return incompleteTodos + ' items left'
      }
    }
  },
  methods: {
    addTodo () {
      if (this.newTodo.trim().length === 0) {
        return
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false
      })

      this.newTodo = ''
      this.idForTodo++
    },
    removeTodo (index) {
      this.todos.splice(index, 1)
    },
    finishedEdit ({ index, todo }) {
      this.todos.splice(index, 1, todo)
    },
    checkAllTodos () {
      this.todos.forEach(todo => { todo.completed = event.target.checked })
    },
    clearCompleted () {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 10px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 10px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  button {
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:hover {
      background: lightgreen;
    }

    &:focus {
      outline: none;
    }
  }

  .active {
    background: lightgreen;
  }
</style>
