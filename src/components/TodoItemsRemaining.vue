<template>
  <div class="extra-container">
    <div>
      <label>
        <input
          type="checkbox"
          :checked="!willCheckAll"
          @change="willCheckAllTodos"
        >
        Check All
      </label>
    </div>
    <div>
      {{ incompleteTodosDisplay }}
    </div>
  </div>
</template>

<script>
import EventBus from '../eventBus/event-bus'

export default {
  name: 'todo-items-remaining',
  props: {
    areAllComplete: {
      type: Boolean,
      required: true
    },
    incompleteTodos: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      'willCheckAll': !this.areAllComplete
    }
  },
  computed: {
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
    willCheckAllTodos () {
      this.willCheckAll = !this.willCheckAll
      EventBus.$emit('checkAllTodos')
    }
  }
}
</script>
