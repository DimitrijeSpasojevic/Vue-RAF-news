<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">Web programiranje</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <router-link :to="{name: 'Categories'}" tag="a" class="nav-link" :class="{active: this.$router.currentRoute.name === 'Categories'}">Categories</router-link>
            </li>
            <li class="nav-item">
              <router-link :to="{name: 'Articles'}" tag="a" class="nav-link" :class="{active: this.$router.currentRoute.name === 'Articles'}">Articles</router-link>
            </li>
            <li class="nav-item" v-if="isAdminUser">
              <router-link :to="{name: 'Users'}" tag="a" class="nav-link" :class="{active: this.$router.currentRoute.name === 'Users'}">Users</router-link>
            </li>
          </ul>
          <form v-if="canLogout" class="d-flex" @submit.prevent="logout">
            <button class="btn btn-outline-secondary" type="submit">Logout</button>
          </form>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>

export default {
  name: "Navbar",
  computed: {
    canLogout() {
      return this.$route.name !== 'Login';
    },
    isAdminUser(){
      if(this.$route.name !== "Login")
        return "Admin" === localStorage.getItem("userType")
      return false
    }
  },
  methods: {
    logout() {
      localStorage.removeItem('jwt');
      localStorage.removeItem('userType');
      this.$router.push({name: 'Login'});
    }
  },
}
</script>

<style scoped>

</style>
