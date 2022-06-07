<template>
  <div class="articles">
    <h1 class="mt-4">Users</h1>

    <div class="row">
      <div class="col-4">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Title</th>
            <th scope="col">Content</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="user in users" :key="user.id" @click="selectedUser = user">
            <th scope="row">{{ user.id }}</th>
            <td>{{ user.firstName }}</td>
            <td>{{ user.lastName }}</td>
          </tr>
          </tbody>
        </table>
      </div>
      <div class="col-6">
        <User v-if="selectedUser" :user="selectedUser"></User>
      </div>
    </div>
  </div>
</template>

<script>
import User from "../components/User";

export default {
  name: "Users",
  components: {User},
  data() {
    return {
      selectedUser: null,
      users: []
    }
  },
  created() {
    this.$axios.get('/api/users').then((response) => {
      this.users = response.data;
      console.log(this.users)
    });
  },
}
</script>
