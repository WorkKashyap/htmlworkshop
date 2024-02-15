<template>
  <div>
    <div v-for="(row, index) in chunkedUsers" :key="index" class="row">
      <div v-for="user in row" :key="user.id" class="col-md-4">
        <div class="card" data-bs-toggle="modal" :data-bs-target="'#exampleModal_' + user.id">
          <div class="card-body">
            <h5 class="card-title">{{ user.name }}</h5>
            <p class="card-text">{{ user.company.name }}</p>
          </div>
        </div>
      </div>
    </div>
    <!-- Modals -->
    <div v-for="user in users" :key="'modal_' + user.id" class="modal fade" :id="'exampleModal_' + user.id" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">User Email</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <p>{{ user.email }} <button @click="toggleDetails(user.id)" class="btn btn-sm btn-outline-primary">{{ user.showDetails ? '-' : '+' }}</button></p>
            <div v-if="user.showDetails">
              <p><strong>Phone:</strong> {{ user.phone }}</p>
              <p><strong>Website:</strong> {{ user.website }}</p>
              <p><strong>Company Details:</strong></p>
              <p>{{ user.company.catchPhrase }}</p>
              <p>{{ user.company.bs }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: []
    };
  },
  computed: {
    chunkedUsers() {
      const chunkSize = 3;
      const chunkedArray = [];
      for (let i = 0; i < this.users.length; i += chunkSize) {
        chunkedArray.push(this.users.slice(i, i + chunkSize));
      }
      return chunkedArray;
    }
  },
  mounted() {
    this.fetchUsers();
  },
  methods: {
    async fetchUsers() {
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      this.users = await response.json();
      // Add showDetails property to each user
      this.users.forEach(user => {
        user.showDetails = false;
      });
    },
    toggleDetails(userId) {
      const user = this.users.find(u => u.id === userId);
      user.showDetails = !user.showDetails;
    }
  }
};
</script>

<style>
.card {
  cursor: pointer;
  margin-bottom: 10px;
}

.btn-sm {
  font-size: 0.1rem; /* Adjust the size as needed */
}
</style>
