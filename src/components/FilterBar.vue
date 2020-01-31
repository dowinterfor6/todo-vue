<template>
  <div class="filter-container">
    <div>
      <transition name="fade">
        <button
          v-if="areAnyComplete"
          @click="willClearCompleted"
        >
          Clear Completed
        </button>
      </transition>
    </div>

    <div>
      <button
        :class="{ active: filter === 'all' }"
        @click="changeFilter('all')"
      >
        All
      </button>
      <button
        :class="{ active: filter === 'incomplete' }"
        @click="changeFilter('incomplete')"
      >
        Incomplete
      </button>
      <button
        :class="{ active: filter === 'completed' }"
        @click="changeFilter('completed')"
      >
        Completed
      </button>
    </div>
  </div>
</template>

<script>
import EventBus from '../eventBus/event-bus'

export default {
  name: 'filter-bar',
  props: {
    areAnyComplete: {
      type: Boolean,
      required: true
    },
    filter: {
      type: String,
      required: true
    }
  },
  methods: {
    willClearCompleted () {
      EventBus.$emit('clearCompleted')
    },
    changeFilter (nextFilterState) {
      EventBus.$emit('changeFilter', nextFilterState)
    }
  }
}
</script>

<style lang="scss">
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
