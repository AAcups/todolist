<template>
  <div class="todos-container">
    <div class="todos-box">
      <div class="header">
        <h2>我的 Todo 列表</h2>
        <button @click="logout" class="logout-btn">退出</button>
      </div>

      <div class="add-todo">
        <input 
          v-model="newTodo" 
          @keyup.enter="addTodo"
          placeholder="输入新的 Todo..."
        />
        <button @click="addTodo" :disabled="!newTodo.trim()">添加</button>
      </div>

      <div v-if="loading" class="loading">加载中...</div>

      <div v-else class="todo-list">
        <div 
          v-for="todo in todos" 
          :key="todo.id" 
          class="todo-item"
        >
          <span>{{ todo.content }}</span>
          <button @click="deleteTodo(todo.id)" class="delete-btn">删除</button>
        </div>

        <div v-if="todos.length === 0" class="empty">
          暂无 Todo，添加一个吧！
        </div>
      </div>

      <p v-if="error" class="error">{{ error }}</p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import api from '../utils/api'

export default {
  setup() {
    const router = useRouter()
    const todos = ref([])
    const newTodo = ref('')
    const loading = ref(false)
    const error = ref('')

    const fetchTodos = async () => {
      loading.value = true
      try {
        const response = await api.get('/api/todos')
        todos.value = response.data
      } catch (err) {
        if (err.response?.status === 401) {
          localStorage.removeItem('token')
          router.push('/login')
        } else {
          error.value = '获取列表失败'
        }
      } finally {
        loading.value = false
      }
    }

    const addTodo = async () => {
      if (!newTodo.value.trim()) return

      try {
        const response = await api.post('/api/todos', {
          content: newTodo.value
        })
        todos.value.unshift(response.data)
        newTodo.value = ''
      } catch (err) {
        error.value = '添加失败'
      }
    }

    const deleteTodo = async (id) => {
      try {
        await api.delete(`/api/todos/${id}`)
        todos.value = todos.value.filter(t => t.id !== id)
      } catch (err) {
        error.value = '删除失败'
      }
    }

    const logout = () => {
      localStorage.removeItem('token')
      router.push('/login')
    }

    onMounted(() => {
      fetchTodos()
    })

    return {
      todos,
      newTodo,
      loading,
      error,
      addTodo,
      deleteTodo,
      logout
    }
  }
}
</script>

<style scoped>
.todos-container {
  width: 100%;
  padding: 20px;
}

.todos-box {
  background: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

h2 {
  margin: 0;
  color: #333;
}

.logout-btn {
  padding: 8px 16px;
  background: #e74c3c;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.add-todo {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.add-todo input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  font-size: 14px;
}

.add-todo button {
  padding: 10px 20px;
  background: #667eea;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.add-todo button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.loading {
  text-align: center;
  color: #666;
  padding: 20px;
}

.todo-list {
  max-height: 400px;
  overflow-y: auto;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  border-bottom: 1px solid #eee;
}

.todo-item:last-child {
  border-bottom: none;
}

.delete-btn {
  padding: 5px 10px;
  background: #e74c3c;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  font-size: 12px;
}

.empty {
  text-align: center;
  color: #999;
  padding: 40px 0;
}

.error {
  color: #e74c3c;
  text-align: center;
  margin-top: 10px;
  font-size: 14px;
}
</style>
