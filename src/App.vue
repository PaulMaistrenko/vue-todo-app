<script>
  import todos from './data/todos';
  import StatusFilter from './components/StatusFilter.vue';

  export default {
    components: {
      StatusFilter,
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
      }
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

        <section class="todoapp__main" data-cy="TodoList">
          <div
            data-cy="Todo"
            v-for="todo, index of todos"
            :key="todo.id"
            class="todo"
            :class="{completed: todo.completed}"
          >
            <label class="todo__status-label">
              <input
                data-cy="TodoStatus"
                type="checkbox"
                class="todo__status"
                v-model="todo.completed"
              />
            </label>

            <form v-if="false">
              <input
                data-cy="TodoTitleField"
                type="text"
                class="todo__title-field"
                placeholder="Empty todo will be deleted"
                value="Todo is being edited now"
              />
            </form>

            <template v-else>
              <span data-cy="TodoTitle" class="todo__title">{{ todo.title }}</span>
              <button type="button" class="todo__remove" data-cy="TodoDelete" @click="todos.splice(index,1)">
                ×
              </button>
            </template>

            
          </div>

          <!--<div data-cy="Todo" class="todo">
            <label class="todo__status-label">
              <input
                data-cy="TodoStatus"
                type="checkbox"
                class="todo__status"
              />
            </label>

            <span data-cy="TodoTitle" class="todo__title">
              Not Completed Todo
            </span>

            <button type="button" class="todo__remove" data-cy="TodoDelete">
              ×
            </button>
          </div>

          <div data-cy="Todo" class="todo">
            <label class="todo__status-label">
              <input
                data-cy="TodoStatus"
                type="checkbox"
                class="todo__status"
              />
            </label>

            <form>
              <input
                data-cy="TodoTitleField"
                type="text"
                class="todo__title-field"
                placeholder="Empty todo will be deleted"
                value="Todo is being edited now"
              />
            </form>
          </div>

          <div data-cy="Todo" class="todo">
            <label class="todo__status-label">
              <input
                data-cy="TodoStatus"
                type="checkbox"
                class="todo__status"
              />
            </label>

            <span data-cy="TodoTitle" class="todo__title">
              Todo is being saved now
            </span>

            <button type="button" class="todo__remove" data-cy="TodoDelete">
              ×
            </button>
          </div>-->
        </section>

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
</style>