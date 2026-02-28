<template>
  <div class="login-container">
    <div class="login-box">
      <h2>{{ isLogin ? '登录' : '注册' }}</h2>
      
      <form @submit.prevent="handleSubmit">
        <div class="form-group">
          <input 
            v-model="username" 
            type="text" 
            placeholder="用户名"
            required
          />
        </div>
        
        <div class="form-group">
          <input 
            v-model="password" 
            type="password" 
            placeholder="密码"
            required
          />
        </div>

        <button type="submit" :disabled="loading">
          {{ loading ? '处理中...' : (isLogin ? '登录' : '注册') }}
        </button>
      </form>

      <p class="toggle-text">
        {{ isLogin ? '没有账号？' : '已有账号？' }}
        <a href="#" @click.prevent="isLogin = !isLogin">
          {{ isLogin ? '立即注册' : '立即登录' }}
        </a>
      </p>

      <p v-if="error" class="error">{{ error }}</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import api from '../utils/api'

export default {
  setup() {
    const router = useRouter()
    const username = ref('')
    const password = ref('')
    const isLogin = ref(true)
    const loading = ref(false)
    const error = ref('')

    const handleSubmit = async () => {
      error.value = ''
      loading.value = true

      try {
        const endpoint = isLogin.value ? '/api/login' : '/api/register'
        const response = await api.post(endpoint, {
          username: username.value,
          password: password.value
        })

        if (isLogin.value) {
          localStorage.setItem('token', response.data.token)
          router.push('/todos')
        } else {
          error.value = '注册成功！请登录'
          isLogin.value = true
        }
      } catch (err) {
        error.value = err.response?.data?.error || '操作失败，请重试'
      } finally {
        loading.value = false
      }
    }

    return {
      username,
      password,
      isLogin,
      loading,
      error,
      handleSubmit
    }
  }
}
</script>

<style scoped>
.login-container {
  width: 100%;
  padding: 20px;
}

.login-box {
  background: white;
  padding: 40px;
  border-radius: 10px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
}

h2 {
  text-align: center;
  margin-bottom: 30px;
  color: #333;
}

.form-group {
  margin-bottom: 20px;
}

input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 5px;
  font-size: 14px;
  transition: border-color 0.3s;
}

input:focus {
  outline: none;
  border-color: #667eea;
}

button {
  width: 100%;
  padding: 12px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: opacity 0.3s;
}

button:hover:not(:disabled) {
  opacity: 0.9;
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.toggle-text {
  text-align: center;
  margin-top: 20px;
  color: #666;
}

.toggle-text a {
  color: #667eea;
  text-decoration: none;
}

.error {
  color: #e74c3c;
  text-align: center;
  margin-top: 15px;
  font-size: 14px;
}
</style>
