<template>
  <div id="app" class="gradient-background">
    <div class="container">
      <h1>What are you doing today?</h1>
      <input v-model="newTask" @keyup.enter="addTask" placeholder="New task" />
      <div class="task-options">
        <button class="all-btn" @click="filterTasks('all')">All</button>
        <button class="completed-btn" @click="filterTasks('completed')">Completed</button>
        <button class="not-completed-btn" @click="filterTasks('not-completed')">Not Completed</button>
      </div>
      <ul>
        <li v-for="(task, index) in filteredTasks" :key="index">
          <div class="task-container">
            <div class="task-header">
              <span :class="{ completed: task.completed }">{{ task.text }}</span>
              <div>
                <button @click="completeTask(index)">
                  <i class="bx" :class="{ 'bx-check check': task.completed, 'bx-checkbox check': !task.completed }"></i>
                </button>
                <button @click="editTask(index)"><i class="bx bx-edit edit"></i></button>
                <button @click="removeTask(index)"><i class="bx bx-x delete"></i></button>
              </div>
            </div>
            <!-- <div class="task-details">
              <label>Priority:</label>
              <select v-model="tasks[index].priority">
                <option value="low">Low</option>
                <option value="medium">Medium</option>
                <option value="high">High</option>
              </select>
              <p>Created: {{ tasks[index].createdAt }}</p>
              <p>Last Modified: {{ tasks[index].updatedAt }}</p>
            </div> -->
          </div>
        </li>
      </ul>
      <div class="task-stats">
        <p>Total Tasks: {{ tasks.length }}</p>
        <p>Completed: {{ completedTasks.length }}</p>
        <p>Not completed: {{ notCompletedTasks.length }}</p>
      </div>
    </div>
  </div>
  <footer class="footer">
      <p>© 2024 To-Do-List 1.0 | <a href="https://cjcastrogalante.com" target="_blank"> Carlos José Castro Galante</a>. All rights reserved.</p>
    </footer>
</template>

<script>
import 'boxicons'

export default {
  data() {
    return {
      newTask: '',
      tasks: JSON.parse(localStorage.getItem('tasks')) || [],
      filter: 'all',
      editingIndex: -1,
    };
  },
  computed: {
    filteredTasks() {
      if (this.filter === 'completed') {
        return this.completedTasks;
      } else if (this.filter === 'not-completed') {
        return this.notCompletedTasks;
      } else {
        return this.tasks;
      }
    },
    completedTasks() {
      return this.tasks.filter(task => task.completed);
    },
    notCompletedTasks() {
      return this.tasks.filter(task => !task.completed);
    },
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        if (this.editingIndex !== -1) {
          // Editar tarea existente
          this.tasks[this.editingIndex].text = this.newTask.trim();
          this.tasks[this.editingIndex].updatedAt = new Date().toLocaleString();
          this.editingIndex = -1;
        } else {
          this.tasks.push({
            text: this.newTask.trim(),
            priority: 'medium',
            completed: false,
            createdAt: new Date().toLocaleString(),
            updatedAt: null,
          });
        }
        this.newTask = '';
        this.saveTasks();
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    completeTask(index) {
      this.tasks[index].completed = !this.tasks[index].completed;
      this.tasks[index].updatedAt = new Date().toLocaleString();
      this.saveTasks();
    },
    editTask(index) {
      this.editingIndex = index;
      this.newTask = this.tasks[index].text;
    },
    cancelEdit() {
      this.editingIndex = -1;
      this.newTask = '';
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },

    filterTasks(filter) {
    this.filter = filter;
  },
  },
};
</script>


<style scoped>
#app {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.container {
  width: 100%;
  max-width: 600px;
  padding: 20px;
  box-sizing: border-box;
  margin-bottom: 4rem;
}

h1 {
  font-weight: bold;
  font-size: 4rem;
  color: #ffffff;
  text-align: center;
  margin: 0;
}

input {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  margin-top: 30px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  border: none;
  outline: none;
}

.task-options {
  display: flex;
  justify-content: space-around;
  margin-bottom: 10px;
  border: none;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 10px 0;
}

.task-container {
  background-color: #fff;
  border: 1px solid #eee;
  border-radius: 5px;
  padding: 15px;
  margin: 10px auto;
  transition: box-shadow 0.3s ease-in-out;
  position: relative;
  border: none;
  color: #000000;
}

.task-container:hover {
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.6);
}

.task-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: none;
}

.task-header button:hover{
  transform: scale(1.1);
}

.completed {
  text-decoration: line-through;
  border: none;
}

.bx {
  font-size: 1.6rem;
  color: #52b788;
  cursor: pointer;
  border: none;
}

.task-details {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: none;
  background: none;
}

.task-details select option{
  background: none;
}

label {
  font-size: 0.8rem;
  color: #555;
  background: none;
}

select {
  padding: 8px;
  border-radius: 10px;
  border: none;
  background-color: #ffffff;
}
.task-stats {
  margin-top: 20px;
  font-size: 15px;
  text-align: center;
  background: none;
  display: flex;
  justify-content: space-between;
  color: #ffffff;
  font-weight: 500;
}

.advanced-stats {
  margin-top: 20px;
  text-align: center;
  background: none;
}

button {
  border: none;
  background: none;
  font-size: 20px;
}

.all-btn{
  color: #4cc9f0;
}

.all-btn:hover{
  background-color: #588796;
  color: #ffffff;
  border-radius: 20px;
}

.completed-btn{
  color: #80ed99;
}

.completed-btn:hover{
  background-color: #73d48a;
  color: #ffffff;
  border-radius: 20px;
}

.not-completed-btn{
  color: #d90429;
}

.not-completed-btn:hover{
  color: #ffffff;
  background-color: #d90429;
  border-radius: 20px;
}

.check{
  color: #80ed99;
}

.delete{
  color:#d90429 ;
}

.edit{
  font-size: 1.4rem;
  transform: translateY(-2px);
}

.footer {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  color: #ffffff;
  text-align: center;
  padding: 10px;
}

</style>
