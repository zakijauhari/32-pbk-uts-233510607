<template>
  <div id="app">
    <div class="app-container">
      <aside class="sidebar">
        <div class="sidebar-header">
          <h1 class="app-title">Manajemen</h1>
          <h1 class="app-title">Tugas</h1>
        </div>
        
        <div class="sidebar-filters">
          <h3 class="section-title">Filter Tugas</h3>
          <ul class="filter-list">
            <li 
              @click="filter = 'all'" 
              :class="filter === 'all' ? 'active-filter' : ''"
            >
              <i class="icon">📋</i> Semua Tugas
              <span class="task-count">{{ todos.length }}</span>
            </li>
            <li 
              @click="filter = 'undone'" 
              :class="filter === 'undone' ? 'active-filter' : ''"
            >
              <i class="icon">⏳</i> Belum Selesai
              <span class="task-count">{{ todos.filter(t => !t.done).length }}</span>
            </li>
            <li 
              @click="filter = 'done'" 
              :class="filter === 'done' ? 'active-filter' : ''"
            >
              <i class="icon">✅</i> Selesai
              <span class="task-count">{{ todos.filter(t => t.done).length }}</span>
            </li>
          </ul>
        </div>
        
        <div class="add-task-section">
          <h3 class="section-title">Tambah Kegiatan</h3>
          <form @submit.prevent="handleAddTask" class="add-task-form">
            <input
              v-model="newTask"
              type="text"
              placeholder="Tulis kegiatan baru..."
              class="form-input"
            />
            <button type="submit" class="form-button">
              <span>Tambah</span>
              <i class="icon">➕</i>
            </button>
          </form>
        </div>
      </aside>

      <main class="main-content">
        <header class="main-header">
          <h2>{{ getCurrentDate() }}</h2>
          <div class="header-actions">
            <button class="action-button" @click="clearCompleted" v-if="todos.some(t => t.done)">
              <i class="icon">🧹</i>
              <span>Hapus Selesai</span>
            </button>
            <button class="action-button" @click="clearAll" v-if="todos.length">
              <i class="icon">🗑️</i>
              <span>Hapus Semua</span>
            </button>
          </div>
        </header>

        <section class="task-list-container">
          <div class="task-list-header">
            <h3>{{ getFilterTitle() }}</h3>
            <p>{{ filteredTodos.length }} item{{ filteredTodos.length !== 1 ? 's' : '' }}</p>
          </div>
          
          <div v-if="filteredTodos.length === 0" class="empty-state">
            <div class="empty-icon">📭</div>
            <p>Tidak ada tugas {{ filter === 'done' ? 'yang selesai' : filter === 'undone' ? 'yang belum selesai' : '' }}</p>
          </div>
          
          <ul class="task-list" v-else>
            <li
              v-for="todo in filteredTodos"
              :key="todo.id"
              class="task-item"
              :class="{ 'task-done': todo.done }"
            >
              <div class="task-left">
                <div class="checkbox-wrapper" @click="toggleTask(todo.id)">
                  <div class="custom-checkbox" :class="{ checked: todo.done }">
                    <i v-if="todo.done" class="checkmark">✓</i>
                  </div>
                </div>
                <span class="task-text">{{ todo.text }}</span>
              </div>
              <div class="task-right">
                <span class="task-date">{{ formatDate(todo.id) }}</span>
                <button @click="removeTask(todo.id)" class="delete-btn">
                  <i class="icon">🗑️</i>
                </button>
              </div>
            </li>
          </ul>
        </section>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const todos = ref([])

const newTask = ref('')
const filter = ref('all')

const handleAddTask = () => {
  if (newTask.value.trim()) {
    todos.value.push({ id: Date.now(), text: newTask.value.trim(), done: false })
    newTask.value = ''
  }
}

const toggleTask = (id) => {
  const task = todos.value.find(t => t.id === id)
  if (task) task.done = !task.done
}

const removeTask = (id) => {
  todos.value = todos.value.filter(t => t.id !== id)
}

const clearCompleted = () => {
  todos.value = todos.value.filter(t => !t.done)
}

const clearAll = () => {
  todos.value = []
}

const filteredTodos = computed(() => {
  if (filter.value === 'done') return todos.value.filter(t => t.done)
  if (filter.value === 'undone') return todos.value.filter(t => !t.done)
  return todos.value
})

const getFilterTitle = () => {
  if (filter.value === 'done') return 'Tugas Selesai'
  if (filter.value === 'undone') return 'Tugas Belum Selesai'
  return 'Semua Tugas'
}

const getCurrentDate = () => {
  const days = ["Minggu", "Senin", "Selasa", "Rabu", "Kamis", "Jumat", "Sabtu"]
  const months = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"]
  
  const now = new Date()
  const day = days[now.getDay()]
  const date = now.getDate()
  const month = months[now.getMonth()]
  const year = now.getFullYear()
  
  return `${day}, ${date} ${month} ${year}`
}

const formatDate = (timestamp) => {
  const date = new Date(timestamp)
  return `${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

:root {
  --primary-color: #4f46e5;
  --primary-dark: #3730a3;
  --primary-light: #818cf8;
  --secondary-color: #10b981;
  --accent-color: #f472b6;
  --text-color: #f8fafc;
  --text-dark: #e2e8f0;
  --text-light: #cbd5e1;
  --bg-dark: #070c2b;
  --bg-darker: #030621;
  --bg-gradient: linear-gradient(135deg, #070c2b, #1e1b4b);
  --bg-light: #111827;
  --sidebar-width: 300px;
  --border-radius: 12px;
  --transition: all 0.3s ease;
  --shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.3);
  --shadow-md: 0 4px 12px rgba(0, 0, 0, 0.3);
  --shadow-lg: 0 8px 24px rgba(0, 0, 0, 0.3);
  --glass-bg: rgba(255, 255, 255, 0.05);
  --glass-border: rgba(255, 255, 255, 0.1);
  --glow: 0 0 15px rgba(79, 70, 229, 0.6);
}

body, html {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: var(--bg-darker);
  color: var(--text-color);
  height: 100%;
  width: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
}

/* Penting: override background putih */
#app {
  background-color: var(--bg-darker);
  min-height: 100vh;
  width: 100%;
}

.app-container {
  display: flex;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  background-color: var(--bg-darker);
  position: relative;
}

/* Futuristic background elements */
.app-container::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: radial-gradient(circle, rgba(79, 70, 229, 0.03) 0%, transparent 80%);
  z-index: 0;
  pointer-events: none;
}

/* Sidebar Styles */
.sidebar {
  width: var(--sidebar-width);
  background: var(--bg-gradient);
  color: var(--text-color);
  padding: 0;
  box-shadow: var(--shadow-lg);
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  overflow-y: auto;
  position: relative;
  z-index: 2;
  border-right: 1px solid var(--glass-border);
}

.sidebar-header {
  padding: 2rem 1.5rem;
  border-bottom: 1px solid var(--glass-border);
  background: rgba(0, 0, 0, 0.2);
  text-align: center;
}

.app-title {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
  background: linear-gradient(to right, var(--primary-light), var(--accent-color));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 0 0 10px rgba(79, 70, 229, 0.3);
  letter-spacing: 1px;
}

.sidebar-filters {
  padding: 1.5rem;
  border-bottom: 1px solid var(--glass-border);
  background: rgba(0, 0, 0, 0.1);
}

.section-title {
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  margin-bottom: 1.2rem;
  color: var(--text-light);
  font-weight: 600;
}

.filter-list {
  list-style: none;
}

.filter-list li {
  display: flex;
  align-items: center;
  padding: 0.8rem 1rem;
  margin-bottom: 0.8rem;
  border-radius: var(--border-radius);
  cursor: pointer;
  transition: var(--transition);
  position: relative;
  backdrop-filter: blur(5px);
  background: var(--glass-bg);
  border: 1px solid transparent;
}

.filter-list li:hover {
  background: rgba(255, 255, 255, 0.1);
  border-color: var(--glass-border);
  transform: translateY(-2px);
  box-shadow: var(--glow);
}

.filter-list li.active-filter {
  background: rgba(79, 70, 229, 0.2);
  font-weight: 600;
  border: 1px solid rgba(79, 70, 229, 0.3);
  box-shadow: 0 0 10px rgba(79, 70, 229, 0.3);
}

.icon {
  margin-right: 0.8rem;
  font-size: 1.2rem;
}

.task-count {
  position: absolute;
  right: 1rem;
  background-color: var(--glass-bg);
  border-radius: 12px;
  padding: 0.15rem 0.6rem;
  font-size: 0.8rem;
  border: 1px solid var(--glass-border);
}

.add-task-section {
  padding: 1.5rem;
  margin-top: auto;
  background: rgba(0, 0, 0, 0.2);
  border-top: 1px solid var(--glass-border);
}

.add-task-form {
  display: flex;
  flex-direction: column;
}

.form-input {
  padding: 1rem;
  border-radius: var(--border-radius);
  border: 1px solid var(--glass-border);
  background-color: rgba(0, 0, 0, 0.2);
  color: var(--text-color);
  margin-bottom: 1rem;
  font-size: 1rem;
  transition: var(--transition);
  backdrop-filter: blur(5px);
}

.form-input::placeholder {
  color: var(--text-light);
  opacity: 0.7;
}

.form-input:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 2px rgba(79, 70, 229, 0.3);
}

.form-button {
  background: linear-gradient(135deg, var(--primary-color), var(--primary-dark));
  color: white;
  border: none;
  padding: 1rem;
  border-radius: var(--border-radius);
  cursor: pointer;
  transition: var(--transition);
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.form-button:hover {
  background: linear-gradient(135deg, var(--primary-dark), var(--primary-color));
  transform: translateY(-2px);
  box-shadow: var(--glow);
}

.form-button span {
  margin-right: 0.5rem;
}

/* Main Content Styles */
.main-content {
  flex: 1;
  padding: 2rem;
  overflow-y: auto;
  background-color: var(--bg-dark); 
  color: var(--text-color);
  display: flex;
  flex-direction: column;
  position: relative;
  z-index: 1;
}

.main-content::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: 
    radial-gradient(circle at 10% 20%, rgba(79, 70, 229, 0.03) 0%, transparent 50%),
    radial-gradient(circle at 90% 80%, rgba(16, 185, 129, 0.03) 0%, transparent 50%);
  pointer-events: none;
  z-index: -1;
}

.main-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid var(--glass-border);
}

.main-header h2 {
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--text-color);
  letter-spacing: 0.5px;
}

.header-actions {
  display: flex;
  gap: 1rem;
}

.action-button {
  display: flex;
  align-items: center;
  padding: 0.7rem 1.2rem;
  background: var(--glass-bg);
  border: 1px solid var(--glass-border);
  backdrop-filter: blur(5px);
  border-radius: var(--border-radius);
  cursor: pointer;
  transition: var(--transition);
  color: var(--text-color);
  box-shadow: var(--shadow-sm);
}

.action-button:hover {
  background: rgba(255, 255, 255, 0.1);
  transform: translateY(-2px);
  box-shadow: var(--glow);
  border-color: var(--primary-light);
}

.action-button i {
  margin-right: 0.5rem;
}

.task-list-container {
  background: var(--glass-bg);
  border-radius: var(--border-radius);
  box-shadow: var(--shadow-md);
  overflow: hidden;
  flex: 1;
  border: 1px solid var(--glass-border);
  backdrop-filter: blur(10px);
  display: flex;
  flex-direction: column;
}

.task-list-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  border-bottom: 1px solid var(--glass-border);
  background: rgba(0, 0, 0, 0.2);
}

.task-list-header h3 {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--text-color);
}

.task-list-header p {
  color: var(--text-light);
  font-size: 0.9rem;
}

.task-list {
  padding: 1rem;
  list-style: none;
  overflow-y: auto;
  flex: 1;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.2rem;
  margin-bottom: 1rem;
  background: rgba(255, 255, 255, 0.03);
  border-radius: var(--border-radius);
  box-shadow: var(--shadow-sm);
  transition: var(--transition);
  border-left: 4px solid var(--primary-color);
  border: 1px solid var(--glass-border);
  color: var(--text-color);
}

.task-item:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
  border-color: var(--primary-light);
}

.task-item.task-done {
  border-left-color: var(--secondary-color);
  opacity: 0.7;
}

.task-left {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex: 1;
}

.checkbox-wrapper {
  cursor: pointer;
}

.custom-checkbox {
  width: 24px;
  height: 24px;
  border: 2px solid var(--glass-border);
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: var(--transition);
  background: rgba(0, 0, 0, 0.2);
}

.custom-checkbox:hover {
  border-color: var(--primary-color);
  box-shadow: 0 0 5px rgba(79, 70, 229, 0.5);
}

.custom-checkbox.checked {
  background-color: var(--secondary-color);
  border-color: var(--secondary-color);
  box-shadow: 0 0 8px rgba(16, 185, 129, 0.5);
}

.checkmark {
  color: white;
  font-size: 0.9rem;
  font-weight: bold;
}

.task-text {
  font-size: 1rem;
  color: var(--text-color);
  transition: var(--transition);
}

.task-done .task-text {
  text-decoration: line-through;
  color: var(--text-light);
}

.task-right {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.task-date {
  font-size: 0.85rem;
  color: var(--text-light);
  background: rgba(0, 0, 0, 0.2);
  padding: 0.3rem 0.7rem;
  border-radius: 8px;
  border: 1px solid var(--glass-border);
}

.delete-btn {
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid var(--glass-border);
  color: var(--text-light);
  cursor: pointer;
  transition: var(--transition);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0.7;
  padding: 0.3rem;
  border-radius: 8px;
}

.task-item:hover .delete-btn {
  opacity: 1;
}

.delete-btn:hover {
  color: var(--accent-color);
  background: rgba(244, 114, 182, 0.1);
  border-color: var(--accent-color);
  box-shadow: 0 0 8px rgba(244, 114, 182, 0.3);
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 4rem 2rem;
  color: var(--text-light);
  flex: 1;
}

.empty-icon {
  font-size: 4rem;
  margin-bottom: 1.5rem;
  opacity: 0.5;
  text-shadow: 0 0 15px rgba(79, 70, 229, 0.5);
}

/* Animasi dan efek */
@keyframes glow {
  0% { box-shadow: 0 0 5px rgba(79, 70, 229, 0.3); }
  50% { box-shadow: 0 0 15px rgba(79, 70, 229, 0.5); }
  100% { box-shadow: 0 0 5px rgba(79, 70, 229, 0.3); }
}

.filter-list li.active-filter {
  animation: glow 2s infinite;
}

/* Responsivitas */
@media (max-width: 1024px) {
  .app-container {
    flex-direction: column;
    height: 100vh;
    width: 100vw;
    overflow: hidden;
  }
  
  .sidebar {
    width: 100%;
    max-height: 40vh;
    border-right: none;
    border-bottom: 1px solid var(--glass-border);
  }
  
  .main-content {
    height: 60vh;
    overflow-y: auto;
  }
}

/* Scrollbar styling */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb {
  background: rgba(79, 70, 229, 0.3);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(79, 70, 229, 0.5);
}
</style>