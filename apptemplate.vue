<template>
  <div id="app">
    <!-- Display login if user is not logged in -->
    <login v-if="!isLoggedIn" @login-success="handleLoginSuccess" />
    
    <!-- Display header and other components if user is logged in -->
    <div v-if="isLoggedIn">
      <header-nav @logout="logout" />
      <div>Session expires in: {{ formattedTimeLeft }}</div> <!-- Show session countdown -->
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue';
import Login from './components/Login.vue';
import HeaderNav from './components/Header.vue';
import { useRouter } from 'vue-router';

export default {
  components: {
    Login,
    HeaderNav,
  },
  setup() {
    const isLoggedIn = ref(false); // Default to not logged in
    const sessionTimer = ref(null); // Reference for session timer
    const timeLeft = ref(0); // Time left for session expiration
    const router = useRouter(); // For redirection

    const startSessionTimer = () => {
      timeLeft.value = 5 * 60 * 1000; // 5 minutes in milliseconds

      // Set up a timer to count down
      const countDown = () => {
        timeLeft.value -= 1000; // Decrease the time every second

        if (timeLeft.value <= 0) {
          logout(); // If time reaches zero, log out
        } else {
          sessionTimer.value = setTimeout(countDown, 1000); // Continue counting down
        }
      };

      sessionTimer.value = setTimeout(countDown, 1000); // Start the countdown
    };

    const handleLoginSuccess = async (token) => {
      // Store the token in sessionStorage
      sessionStorage.setItem('token', token);
      isLoggedIn.value = true; // Set to logged in

      // Start the session timer
      startSessionTimer();

      await nextTick(); // Ensure state updates before redirecting
      router.push({ name: 'header' }); // Redirect to header after login
    };

    const logout = () => {
      isLoggedIn.value = false; // Set to logged out
      sessionStorage.removeItem('token'); // Clear session storage
      if (sessionTimer.value) {
        clearTimeout(sessionTimer.value); // Clear the session timer
      }
      router.push({ name: 'login' }); // Redirect to login on logout
    };

    const formattedTimeLeft = computed(() => {
      const minutes = Math.floor(timeLeft.value / 60000); // Convert to minutes
      const seconds = Math.floor((timeLeft.value % 60000) / 1000); // Convert to seconds
      return `${minutes}m ${seconds}s`; // Display formatted time
    });

    onMounted(() => {
      const token = sessionStorage.getItem('token'); // Check if there's a token
      if (token) {
        isLoggedIn.value = true; // Set to logged in
        startSessionTimer(); // Start the session timer
      } else {
        isLoggedIn.value = false; // Set to logged out
        router.push({ name: 'login' }); // Redirect to login if not logged in
      }
    });

    onUnmounted(() => {
      if (sessionTimer.value) {
        clearTimeout(sessionTimer.value); // Clear the session timer to prevent memory leaks
      }
    });

    return {
      isLoggedIn,
      handleLoginSuccess,
      logout,
      formattedTimeLeft, // Display the formatted time left for session expiration
    };
  },
};
</script>

<style scoped>
/* Any necessary styles for the component */
</style>
