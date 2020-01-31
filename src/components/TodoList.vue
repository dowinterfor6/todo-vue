<template>
  <div class="todo-list-wrapper">
    <input-fields
    >
    </input-fields>

    <filter-bar
      :areAnyComplete="areAnyComplete"
      :filter="filter"
    >
    </filter-bar>

    <todo-items-remaining
      :areAllComplete="!areAnyIncomplete"
      :incompleteTodosLength="incompleteTodos.length"
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
import TodoItem from './TodoItem'
import TodoItemsRemaining from './TodoItemsRemaining'
import FilterBar from './FilterBar'
import InputFields from './InputFields'

export default {
  name: 'TodoList',
  components: {
    TodoItem,
    TodoItemsRemaining,
    FilterBar,
    InputFields
  },
  data () {
    return {
      idForNewTodo: 3,
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
    EventBus.$on('addTodo', data => this.addTodo(data))
  },
  computed: {
    incompleteTodos () {
      return this.todos.filter(todo => !todo.completed)
    },
    completeTodos () {
      return this.todos.filter(todo => todo.completed)
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
          return this.incompleteTodos
        case 'completed':
          return this.completeTodos
      }
    }
  },
  methods: {
    addTodo (title) {
      if (title.trim().length === 0) {
        return
      }

      this.todos.push({
        id: this.idForNewTodo,
        title,
        completed: false,
        editing: false
      })

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
