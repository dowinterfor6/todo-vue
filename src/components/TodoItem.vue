<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input
        type="checkbox"
        v-model="completed"
        @change="doneEdit"
      >
      <div
        class="todo-item-label"
        @dblclick="editTodo"
        v-if="!editing"
        :class="{ completed : completed }"
      >
        {{ title }}
      </div>
      <input
        class="todo-item-edit"
        type="text"
        v-model="title"
        v-else
        @blur="doneEdit"
        @keyup.enter="doneEdit"
        @keyup.esc="cancelEdit"
        v-focus
      >
    </div>
    <div>
      <button @click="pluralize">
        Plural
      </button>
      <span
        class="remove-item"
        @click="removeTodo(index)"
      >
        &times;
      </span>
    </div>
  </div>
</template>

<script>
import EventBus from '../eventBus/event-bus'

export default {
  name: 'todo-item',
  props: {
    todo: {
      type: Object,
      required: true
    },
    index: {
      type: Number,
      retuired: true
    },
    checkAll: {
      type: Boolean,
      required: true
    }
  },
  data () {
    return {
      'id': this.todo.id,
      'title': this.todo.title,
      'completed': this.todo.completed,
      'editing': this.todo.editing,
      'beforeEditCache': ''
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  created () {
    EventBus.$on('pluralize', this.handlePluralize)
  },
  beforeDestroy () {
    EventBus.$off('pluralize', this.handlePluralize)
  },
  watch: {
    checkAll () {
      this.completed = this.checkAll ? true : this.todo.completed
    }
  },
  methods: {
    removeTodo (index) {
      EventBus.$emit('removedTodo', index)
    },
    editTodo () {
      this.beforeEditCache = this.title
      this.editing = true
    },
    doneEdit () {
      if (this.title.trim().length === 0) {
        this.title = this.beforeEditCache
      }

      this.editing = false

      EventBus.$emit('finishedEdit', {
        'index': this.index,
        'todo': {
          'id': this.id,
          'title': this.title,
          'completed': this.completed,
          'editing': this.editing
        }
      })
    },
    cancelEdit () {
      this.title = this.beforeEditCache
      this.editing = false
    },
    pluralize () {
      EventBus.$emit('pluralize')
    },
    handlePluralize () {
      this.title = this.title + 's'

      EventBus.$emit('finishedEdit', {
        'index': this.index,
        'todo': {
          'id': this.id,
          'title': this.title,
          'completed': this.completed,
          'editing': this.editing
        }
      })
    }
  }
}
</script>

<style lang="scss">
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css");

  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
  }

  .remove-item {
    cursor: pointer;
    margin-left: 14px;

    &:hover {
      color: black;
    }
  }

  .todo-item-left {
    display: flex;
    align-items: center;
  }

  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
      outline: none;
    }
  }

  .completed {
    text-decoration: line-through;
    color: grey;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
