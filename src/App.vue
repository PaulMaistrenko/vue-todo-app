<script>
  import todos from './data/todos';
  import StatusFilter from './components/StatusFilter.vue';
  import TodoItem from './components/TodoItem.vue';

  export default {
    components: {
      StatusFilter,
      TodoItem,
    },
    data() {
      let todos = [];
      const jsonData = localStorage.getItem('todos') || '[]';

      try {
        todos = JSON.parse(jsonData);
      } catch(err){}

      return {
        todos,
        title: '',
        status: 'all',
      }
    },
    computed: {
      activeTodos() {
        return this.todos.filter(todo => !todo.completed);
      },
      completedTodos() {
        return this.todos.filter(todo => todo.completed);
      },
      visibleTodos() {
        switch(this.status) {
          case 'active': 
            return this.activeTodos;
          case 'completed':
            return this.completedTodos;
          default:
            return this.todos;
        }
      },
    },
    watch: {
      todos: {
        deep: true,
        handler() {
          localStorage.setItem('todos', JSON.stringify(this.todos));
        }
      }
    },
    methods: {
      handleSubmit() {
        this.todos.push({
          id: Date.now(),
          title: this.title,
          complete: false,
        });

        this.title = '';
      }
    }
  }
</script>

<template>
    <div class="todoapp">
      <h1 class="todoapp__title">todos</h1>

      <div class="todoapp__content">
        <header class="todoapp__header">
          <button
            type="button"
            data-cy="ToggleAllButton"
            class="todoapp__toggle-all"
            :class="{ active: activeTodos.length === 0 }"
          >
          </button>

          <form @submit.prevent="handleSubmit">
            <input
              data-cy="NewTodoField"
              type="text"
              class="todoapp__new-todo"
              placeholder="What needs to be done?"
              v-model="title"
            />
          </form>
        </header>

        <TransitionGroup
          name="list"
          tag="section"
          class="todoapp__main"
          data-cy="TodoList"
        >
          <TodoItem 
            v-for="todo, index of visibleTodos"
            :key="todo.id"
            :todo="todo"
            @update="Object.assign(todo, $event)"
            @delete="todos.splice(todos.indexOf(todo), 1)"
          />
        </TransitionGroup>

        <footer class="todoapp__footer" data-cy="Footer">
          <span class="todo-count" data-cy="TodosCounter">
            {{ activeTodos.length }} items left
          </span>

          <StatusFilter v-model="status"/>

          <button
            type="button"
            class="todoapp__clear-completed"
            data-cy="ClearCompletedButton"
            v-if="activeTodos.length > 0"
          >
            Clear completed
          </button>
        </footer>
      </div>
    </div>
</template>

<style>
.list-enter-active,
.list-leave-active {
   transition: all 0.5s ease;
   max-height: 60px;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  max-height: 0;
  transform: scaleY(0);
}
</style>