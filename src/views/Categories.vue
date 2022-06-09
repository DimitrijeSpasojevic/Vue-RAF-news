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
          <tbody ref="tableBodyCategory">
          <tr v-for="category in categories" :key="category.id" @click="selectedCategory = category">
            <th scope="row">{{ category.id }}</th>
            <td @click="showNewsFromCategory(category.name)">{{ category.name }}</td>
            <td>{{ category.description }}</td>
            <td><button class="btn-close" @click = "deleteCategory(category)"/></td>
            <td><button class="btn-light" @click = "preUpdateCategory(category)"> Izmeni </button></td>
          </tr>
          </tbody>
        </table>

        <ul class="pagination">
          <li class="page-item"><a class="page-link" @click = "getCategoriesByPage('prev')">Previous</a></li>
          <li class="page-item"><a class="page-link" @click = "getCategoriesByPage('next')" >Next</a></li>
        </ul>
      </div>
      <div class="col-6">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">Naslov Vesti</th>
            <th scope="col">Tekst</th>
            <th scope="col">Kategorija</th>
          </tr>
          </thead>
          <tbody ref="tableBodyArticles">
          <tr v-for="article in articles" :key="article.id">
            <th scope="row">{{ article.title }}</th>
            <td>{{ article.text }}</td>
            <td>{{ article.categoryName }}</td>
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
      <input ref="categoryNameInput" v-model="ime"  type="text" required placeholder="Enter name"/><br><br>
      <input ref="categoryDescriptionInput" v-model="opis"  type="text" required placeholder="Enter description"/><br>
      <br>
      <button ref="btnAdd" @click="addNewCategory(ime,opis)" class="btn btn-primary m-1">Dodaj</button>
      <button ref="btnUpdate" @click="updateCategory(ime,opis)" class="btn btn-primary m-1" style="visibility: hidden">Izmeni</button>
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
      articles:[],
      ime: '',
      opis: '',
      articlesInCategory: Number,
      pageNum: 0
    }
  },
  methods: {

    showNewsFromCategory(categoryName){
      this.$axios.get('/api/news/category/' + categoryName + '/0').then((response) => {
        this.articles = response.data;
      });
    },
    deleteCategory(category) {

      this.$axios.get('/api/news/category/' + category.name + '/0').then((response) => {
        this.articlesInCategory = response.data.length;
      });

      if(this.articlesInCategory == 0){
        this.$axios.delete('/api/category/' + category.name).then(() => {
          const index = this.categories.indexOf(category);
          if (index > -1) {
            this.categories.splice(index, 1);
          }
        });
      }
    },
    getCategoriesByPage(action){
      if(action == 'prev'){
        if(this.pageNum != 0)
          this.pageNum--;
      }else{
        this.pageNum++;
      }
      this.$axios.get('/api/category/offset/' + this.pageNum).then((response) => {
        this.categories = response.data;
      });
    },
    preUpdateCategory(category) {
      this.selectedCategory = category
      this.$refs.formNewCategory.style.visibility = "visible"
      this.$refs.categoryNameInput.value = category.name
      this.$refs.categoryDescriptionInput.value = category.description
      this.$refs.btnUpdate.style.visibility = "visible"
      this.$refs.btnAdd.disabled = true
    },
    updateCategory(ime, opis){
      const index = this.categories.indexOf(this.selectedCategory);
      this.$axios.put('/api/category', {
        id: this.selectedCategory.id,
        name: ime,
        description: opis
      })
          .then((response) => {
            // this.categories[index] = response.data
            this.categories.splice(index, 1, response.data)
            console.log(response.data)
          })
          .catch(function (error) {
            console.log(error);
          });
      this.$refs.btnAdd.disabled = false
      this.$refs.btnUpdate.style.visibility = "hidden"
      this.$refs.formNewCategory.style.visibility = "hidden"
    },
    showForm() {
      this.$refs.formNewCategory.style.visibility = "visible"
      this.$refs.btnAdd.disabled = false
    },
    addNewCategory(ime, opis) {
      this.$axios.post('/api/category', {
        name: ime,
        description: opis
      })
          .then((response) => {
            this.categories.push(response.data)
          })
          .catch(function (error) {
            console.log(error);
          });
    }
},
  created() {
    this.$axios.get('/api/category/offset/0').then((response) => {
      this.categories = response.data;
    });
  }
}
</script>
