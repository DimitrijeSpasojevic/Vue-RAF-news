<template>
  <div class="categories">
    <h1 class="mt-4">Categories</h1>

    <div class="row">
      <div class="col-6">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Ime</th>
            <th scope="col">Opis kategorije</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="category in categories" :key="category.id" @click="selectedCategory = category">
            <th scope="row">{{ category.id }}</th>
            <td>{{ category.name }}</td>
            <td>{{ category.description }}</td>
            <td><button class="btn-close" @click = "deleteCategory(category)"/></td>
          </tr>
          </tbody>
        </table>
      </div>
      <div class="col-4">
        <Category v-if="selectedCategory" :category="selectedCategory"></Category>
      </div>
    </div><br>
    <Button class="btn-primary" @click="showForm">Dodaj kategoriju</Button><br>
    <br>
    <form ref="formNewCategory" style="visibility: hidden">
      <input v-model="ime"  type="text" required placeholder="Enter name"/><br><br>
      <input v-model="opis"  type="text" required placeholder="Enter description"/><br>
      <br>
      <button @click.prevent="addNewCategory(ime,opis)" class="btn btn-primary">Submit</button>
    </form>

  </div>
</template>

<script>
import Category from "../components/Category";

export default {
  name: "Categories",
  components: {Category},
  data() {
    return {
      selectedCategory: null,
      categories: [],
      ime: '',
      opis :'',
    }
  },
  methods: {

    getCategories(){
      this.$axios.get('/api/category').then((response) => {
        this.categories = response.data;
      });
    },
    deleteCategory(category) {
      this.$axios.delete('/api/category/' + category.name).then(() => {
        const index = this.categories.indexOf(category);
        if (index > -1) {
          this.categories.splice(index, 1);
        }
      });
    },
    showForm(){
      this.$refs.formNewCategory.style.visibility = "visible"
    },
    addNewCategory(ime,opis){

      this.$axios.post('/api/category', {
        name: ime,
        description: opis
      })
          .then(function (response) {
              this.categories.push(response)

          })
          .catch(function (error) {
            console.log(error);
          });
    }
  },
  created() {
    this.$axios.get('/api/category').then((response) => {
      this.categories = response.data;
    });
  },
}
</script>
