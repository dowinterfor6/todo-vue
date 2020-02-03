<template>
  <div class="check-all-container">
    <div>
      <label>
        <input
          type="checkbox"
          :checked="areAllComplete"
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
    incompleteTodosLength: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      'willCheckAll': this.areAllComplete
    }
  },
  computed: {
    incompleteTodosDisplay () {
      const incompleteTodos = this.incompleteTodosLength

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

<style lang="scss">
  .check-all-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 10px;
    border-top: 1px solid lightgrey;
    border-bottom: 1px solid lightgrey;
    padding: 14px 0;
    margin: 14px 0;
  }
</style>
