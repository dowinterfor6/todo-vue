<template>
  <div class="filter-container">
    <div>
      <transition name="fade">
        <button
          v-if="areAnyComplete"
          @click="clearCompleted"
        >
          Clear Completed
        </button>
      </transition>
    </div>

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
  </div>
</template>

<script>
export default {
  name: 'filter-bar',
  props: {
    
  },
  methods: {
    clearCompleted () {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>