/*
* Curso de Engenharia de Software - UniEVANGÉLICA 
* Disciplina de Programação Web 
* Dev: NOME 
* DATA
*/ 
<!-- AddTask.vue -->
<template>
  <div class="add-task">
    <input type="text" v-model="taskName" @keyup.enter="addTask">
    <button @click="addTask">Add Task</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      taskName: ''
    };
  },
  methods: {
    addTask() {
      if (this.taskName.trim() !== '') {
        this.$emit('add-task', this.taskName);
        this.taskName = '';
      }
    }
  }
};
</script>

<!-- Task.vue -->
<template>
  <div class="task">
    <span :class="{ completed: task.completed }">{{ task.name }}</span>
    <button @click="toggleCompletion">{{ task.completed ? 'Undo' : 'Done' }}</button>
    <button @click="removeTask">Remove</button>
  </div>
</template>

<script>
export default {
  props: ['task'],
  methods: {
    toggleCompletion() {
      this.$emit('toggle-completion', this.task.id);
    },
    removeTask() {
      this.$emit('remove-task', this.task.id);
    }
  }
};
</script>
<style scoped>
.completed {
  text-decoration: line-through;
}
</style>

<!-- TaskList.vue -->
<template>
  <div class="task-list">
    <task v-for="task in tasks" :key="task.id" :task="task"
          @toggle-completion="toggleCompletion"
          @remove-task="removeTask"></task>
  </div>
</template>

<script>
import Task from './Task.vue';

export default {
  components: {
    Task
  },
  props: ['tasks'],
  methods: {
    toggleCompletion(id) {
      this.$emit('toggle-completion', id);
    },
    removeTask(id) {
      this.$emit('remove-task', id);
    }
  }
};
</script>

<!-- App.vue -->
<template>
  <div id="app">
    <add-task @add-task="addTask"></add-task>
    <task-list :tasks="tasks"
               @toggle-completion="toggleCompletion"
               @remove-task="removeTask"></task-list>
  </div>
</template>

<script>
import AddTask from './components/AddTask.vue';
import TaskList from './components/TaskList.vue';

export default {
  name: 'App',
  components: {
    AddTask,
    TaskList
  },
  data() {
    return {
      tasks: []
    };
  },
  methods: {
    addTask(name) {
      this.tasks.push({ id: Date.now(), name, completed: false });
    },
    toggleCompletion(id) {
      const task = this.tasks.find(task => task.id === id);
      if (task) {
        task.completed = !task.completed;
      }
    },
    removeTask(id) {
      this.tasks = this.tasks.filter(task => task.id !== id);
    }
  }
};
</script>

<style>
.task-list {
  margin-top: 20px;
}
.task {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.task button {
  margin-left: 10px;
}
</style>
