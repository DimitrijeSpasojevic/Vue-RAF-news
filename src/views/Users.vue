<template>
  <div class="articles">
    <h1 class="mt-4">Users</h1>

    <div class="row">
      <div class="col-6">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">Ime Prezime</th>
            <th scope="col">Email</th>
            <th scope="col">Tip</th>
            <th scope="col"></th>
            <th scope="col">Aktivan</th>
          </tr>
          </thead>
          <tbody ref="tableBodyUser">
          <tr v-for="user in users" :key="user.id" @click="selectedUser = user">
            <th scope="row">{{ user.firstName }} {{ user.lastName }} </th>
            <td>{{ user.email }}</td>
            <td>{{ user.type }}</td>
            <td><button class="btn-light" @click = "showFormForUpdateUser">Izmena</button></td>
            <td v-if="user.type != 'Admin'"><button ref="btnActivation" class="btn-light" @click = "changeActivation(user)"> {{ user.active }} </button></td>
          </tr>
          </tbody>
        </table>

        <ul class="pagination">
          <li class="page-item"><a class="page-link" @click = "getUsersByPage('prev')">Previous</a></li>
          <li class="page-item"><a class="page-link" @click = "getUsersByPage('next')" >Next</a></li>
        </ul>
      </div>

    </div>
    <Button class="btn-primary" @click="showForm">Dodaj korisnika</Button><br><br>
    <Button class="btn-primary" ref="btnUpdateUser" @click="updateUser" style="visibility: hidden">Izmeni korisnika</Button><br><br>

<!--    <Error v-if="isError.length > 0" errText="Email je zauzet, unesite drugi"></Error>-->

    <form ref="formNewUser" style="visibility: hidden">
      <input ref="userFirstNameInput" v-model="firstName"  type="text" required placeholder="Enter first name"/><br><br>
      <input ref="userLastNameInput" v-model="lastName"  type="text" required placeholder="Enter last name"/><br><br>
      <input ref="userEmailInput" v-model="email"  type="text" required placeholder="Enter email"/><br><br>
      <select ref="selectType" v-model="selectedType" required>
        <option  disabled value="">Please select one</option>
        <option> Admin </option>
        <option> ContentCreator </option>
      </select><br><br>
      <input v-if="isUpdateFormActive == false" ref="userPassInput" v-model="password"  type="text" required placeholder="Enter password"/><br><br>
      <input v-if="isUpdateFormActive == false" ref="userRepeatedPassInput" v-model="passwordRepeated"  type="text" required placeholder="Repeat password"/><br><br>
      <button ref="btnAddUser" @click="addNewUser" class="btn btn-primary m-1">Dodaj korisnika</button>
    </form>

  </div>
</template>

<script>

// import Error from "../components/Error";

export default {
  name: "Users",
  // components: {Error},
  data() {
    return {
      selectedUser: null,
      users: [],
      firstName: '',
      lastName: '',
      email: '',
      selectedType: '',
      password: '',
      passwordRepeated: '',
      isUpdateFormActive: false,
      pageNum: 0,
      error: ''
    }
  },
  created() {
    this.$axios.get('/api/users/offset/0').then((response) => {
      this.users = response.data;
      console.log(this.users)
    });
  },
  methods: {
    showForm() {
      this.isUpdateFormActive = false
      this.$refs.formNewUser.style.visibility = "visible"
      this.$refs.btnAddUser.disabled = false
      this.$refs.btnUpdateUser.style.visibility = "hidden"
    },
    showFormForUpdateUser(){
      this.isUpdateFormActive = true
      this.$refs.formNewUser.style.visibility = "visible"
      this.$refs.userFirstNameInput.value = this.selectedUser.firstName
      this.$refs.userLastNameInput.value = this.selectedUser.lastName
      this.$refs.userEmailInput.value = this.selectedUser.email
      this.$refs.selectType.value = this.selectedUser.type
      this.$refs.btnAddUser.disabled = true
      this.$refs.btnUpdateUser.style.visibility = "visible"
    },
    getUsersByPage(action){
      if(action == 'prev'){
        if(this.pageNum != 0)
          this.pageNum--;
      }else{
        this.pageNum++;
      }
      this.$axios.get('/api/users/offset/' + this.pageNum).then((response) => {
        this.users = response.data;
      });
    },
    changeActivation(user){
      const index = this.users.indexOf(user);
      this.$axios.put('/api/users', {
        id: user.id,
        email: user.email,
        firstName: user.firstName,
        lastName: user.lastName,
        type: user.type,
        active: !user.active
      })
          .then((response) => {
            this.users.splice(index, 1, response.data)
          })
          .catch(function (error) {
            console.log(error);
          });
     this.$refs.btnActivation.innerText = 'afs'
    },
    updateUser(){
      const index = this.users.indexOf(this.selectedUser);
      this.$axios.put('/api/users', {
        id: this.selectedUser.id,
        email: this.email,
        firstName: this.firstName,
        lastName: this.lastName,
        type: this.selectedType,
        active: this.selectedUser.active
      })
          .then((response) => {
            this.users.splice(index, 1, response.data)
          })
          .catch(function (error) {
            console.log(error);
          });
      this.$refs.btnAddUser.disabled = false
      this.$refs.btnUpdateUser.style.visibility = "hidden"
      this.$refs.formNewUser.style.visibility = "hidden"
    },
    addNewUser(){
      this.$axios.post('/api/users', {
        email: this.email,
        firstName: this.firstName,
        lastName: this.lastName,
        type: this.selectedType,
        active: "true",
        password: this.password
      })
          .then((response) => {
            this.users.push(response.data)
          })
          .catch(() => {
            this.error = 'desilo se'
            alert("Email je zauzet!");
          });
    },
    isError(){
      return this.error
    }
  }
}
</script>
