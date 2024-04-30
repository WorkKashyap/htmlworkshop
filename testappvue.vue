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
  data() {
    return {
      isLoggedIn: false, // Default to not logged in
      sessionTimer: null, // Reference for session timer
      timeLeft: 0, // Time left for session expiration
    };
  },
  methods: {
    handleLoginSuccess(token) {
      // Store the token in sessionStorage
      sessionStorage.setItem('token', token);
      this.isLoggedIn = true; // Set to logged in

      // Start the session timer
      this.timeLeft = 5 * 60 * 1000; // 5 minutes in milliseconds

      // Set up a timer to count down
      const countDown = () => {
        this.timeLeft -= 1000; // Decrease the time every second

        if (this.timeLeft <= 0) {
          this.logout(); // If time reaches zero, log out
        } else {
          this.sessionTimer = setTimeout(countDown, 1000); // Continue counting down
        }
      };

      this.sessionTimer = setTimeout(countDown, 1000); // Start the countdown

      nextTick(() => {
        this.$router.push({ name: 'header' }); // Redirect to header after login
      });
    },
    logout() {
      this.isLoggedIn = false; // Set to logged out
      sessionStorage.removeItem('token'); // Clear session storage
      if (this.sessionTimer) {
        clearTimeout(this.sessionTimer); // Clear the session timer
      }
      this.$router.push({ name: 'login' }); // Redirect to login on logout
    },
  },
  computed: {
    formattedTimeLeft() {
      const minutes = Math.floor(this.timeLeft / 60000); // Convert to minutes
      const seconds = Math.floor((this.timeLeft % 60000) / 1000); // Convert to seconds
      return `${minutes}m ${seconds}s`; // Display formatted time
    },
  },
  mounted() {
    const token = sessionStorage.getItem('token'); // Check if there's a token
    if (token) {
      this.isLoggedIn = true; // Set to logged in
      // Start the session timer
      this.timeLeft = 5 * 60 * 1000; // 5 minutes in milliseconds

      // Set up a timer to count down
      const countDown = () => {
        this.timeLeft -= 1000; // Decrease the time every second

        if (this.timeLeft <= 0) {
          this.logout(); // If time reaches zero, log out
        } else {
          this.sessionTimer = setTimeout(countDown, 1000); // Continue counting down
        }
      };

      this.sessionTimer = setTimeout(countDown, 1000); // Start the countdown
    } else {
      this.isLoggedIn = false; // Set to logged out
      this.$router.push({ name: 'login' }); // Redirect to login if not logged in
    }
  },
  beforeUnmount() {
    if (this.sessionTimer) {
      clearTimeout(this.sessionTimer); // Clear the session timer to prevent memory leaks
    }
  },
};
</script>

<style scoped>
/* Any necessary styles for the component */
</style>
