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

    <filter-bar
      :areAnyComplete="areAnyComplete"
      :filter="filter"
    >
    </filter-bar>

    <todo-items-remaining
      :areAllComplete="!areAnyIncomplete"
      :incompleteTodos="incompleteTodos"
    >
    </todo-items-remaining>

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
        :checkAll="!areAnyIncomplete"
      >
      </todo-item>
    </transition-group>
  </div>
</template>

<script>
import EventBus from '../eventBus/event-bus'
import Search from './Search'
import TodoItem from './TodoItem'
import TodoItemsRemaining from './TodoItemsRemaining'
import FilterBar from './FilterBar'

export default {
  name: 'TodoList',
  components: {
    Search,
    TodoItem,
    TodoItemsRemaining,
    FilterBar
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
    EventBus.$on('clearCompleted', () => this.clearCompleted())
    EventBus.$on('changeFilter', nextFilterState => this.changeFilter(nextFilterState))
  },
  computed: {
    incompleteTodos () {
      return this.todos.filter(todo => !todo.completed).length
    },
    areAnyIncomplete () {
      return this.todos.some(todo => !todo.completed)
    },
    areAnyComplete () {
      return this.todos.some(todo => todo.completed)
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
    },
    changeFilter (nextFilterState) {
      this.filter = nextFilterState
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

  .filter-container {
    display: flex;
    justify-content: space-between;
  }
</style>
