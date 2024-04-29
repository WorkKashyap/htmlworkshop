<template>
  <nav v-if="token" class="navbar navbar-expand-lg navbar-light bg-light">
    <!-- Navbar content -->
    <button @click="logout" class="btn btn-outline-danger my-2 my-sm-0">Logout</button>
  </nav>
</template>

<script>
import { ref, watch } from 'vue';
import { useRouter } from 'vue-router'; // Router for navigation

export default {
  setup() {
    const token = ref(sessionStorage.getItem('token')); // Initialize with the current token
    const router = useRouter(); // Use Vue Router for navigation

    const logout = () => {
      sessionStorage.removeItem('token'); // Clear the token from session storage
      token.value = null; // Update the reactive state

      router.push({ name: 'login' }); // Redirect to login on logout
    };

    // Watch for changes to the session storage token
    watch(
      () => sessionStorage.getItem('token'),
      (newValue) => {
        token.value = newValue; // Update reactive state when token changes
      },
      { immediate: true } // Watch immediately on component mount
    );

    return {
      token,
      logout, // Provide logout function to the template
    };
  },
};
</script>

<style scoped>
/* Add any additional styles for the navbar */
</style>
