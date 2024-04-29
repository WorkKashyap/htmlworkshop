<template>
  <div>
    <input v-model="username" placeholder="Username" />
    <input v-model="password" type="password" placeholder="Password" />
    <button @click="login">Login</button>
  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

export default {
  setup() {
    const username = ref('');
    const password = ref('');
    const router = useRouter(); // Use Vue Router instance for redirection

    const login = async () => {
      try {
        // API call to log in with username and password
        const response = await axios.post('posturl', {
          userName: username.value,
          password: password.value,
        });

        // Store the token in sessionStorage
        const token = response.data.token;
        sessionStorage.setItem('token', token);

        // Redirect to a named route after successful login
        
        router.push({ name: 'header' ,redirect: 'header' }); // Redirect to the correct route
      } catch (error) {
        // Enhanced error handling with better messaging
        if (error.response && error.response.status === 401) {
          console.error('Login failed: Invalid credentials.');
        } else {
          console.error('Login failed:', error.message);
        }
      }
    };

    return {
      username,
      password,
      login,
    };
  },
};
</script>
