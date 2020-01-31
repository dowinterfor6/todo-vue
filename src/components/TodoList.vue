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
      leave-active-class="animated fadeOutUp"
    >
      <todo-item
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        :todo="todo"
        :index="index"
        :checkAll="!areAllIncomplete"
      >
      </todo-item>
    </transition-group>

    <todo-items-remaining
      :areAllComplete="areAllComplete"
      :incompleteTodos="incompleteTodos"
    >
    </todo-items-remaining>

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
            v-if="areAllComplete"
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
import EventBus from '../eventBus/event-bus'
import Search from './Search'
import TodoItem from './TodoItem'
import TodoItemsRemaining from './TodoItemsRemaining'

export default {
  name: 'TodoList',
  components: {
    Search,
    TodoItem,
    TodoItemsRemaining
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
  created () {
    EventBus.$on('removedTodo', index => this.removeTodo(index))
    EventBus.$on('finishedEdit', data => this.finishedEdit(data))
    EventBus.$on('checkAllTodos', () => this.checkAllTodos())
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
    areAllComplete () {
      return this.todos.every(todo => todo.completed)
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
