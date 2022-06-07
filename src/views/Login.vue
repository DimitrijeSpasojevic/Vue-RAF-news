<template>
  <div class="pt-5">
    <form @submit.prevent="login">
      <div class="form-group">
        <label for="email">Email</label>
        <input v-model="email" type="text" class="form-control" id="email" aria-describedby="emailHelp" placeholder="Enter email">
      </div>
      <div class="form-group">
        <label for="exampleInputPassword1">Password</label>
        <input v-model="password" type="password" class="form-control" id="exampleInputPassword1" placeholder="Password">
      </div>
      <button type="submit" class="btn btn-primary mt-2">Submit</button>
    </form>
    <p ref="errLogin" style="visibility: hidden">Greska pri prijavljivanju</p>
  </div>
</template>

<script>
import jwt_decode from "jwt-decode";

export default {
  name: "Login",
  data() {
    return {
      email: '',
      password: '',
    }
  },
  methods: {
    login() {
      this.$axios.post('/api/users/login', {
        email: this.email,
        password: this.password,
      }).then(response => {
        localStorage.setItem('jwt', response.data.jwt)

        let loggedUser = jwt_decode(localStorage.getItem("jwt"));
        localStorage.setItem('userType', loggedUser.type)

        this.$router.push({name: 'Articles'});
      }).catch(
          this.$refs.errLogin.style.visibility = "visible"
      )
    }
  },
}
</script>

<style scoped>

</style>
