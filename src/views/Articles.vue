<template>
  <div class="articles">
    <h1 class="mt-4">Articles</h1>

    <div class="row">
      <div class="col-6">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Title</th>
            <th scope="col">Author</th>
            <th scope="col">Date</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="article in articles" :key="article.id" @click="selectedArticle = article">
            <th scope="row">{{ article.id }}</th>
            <td>{{ article.title }}</td>
            <td>{{ article.authorEmail }}</td>
            <td>{{ new Date(article.date).toLocaleDateString('en-US') }}</td>
            <td><button class="btn-close" @click = "deleteNews(article)"/></td>
            <td><button class="btn-light" @click = "preUpdateArticle(article)"> Izmeni </button></td>
          </tr>
          </tbody>
        </table>


          <ul class="pagination">
            <li class="page-item"><a class="page-link" @click = "getArticlesByPage('prev')">Previous</a></li>
            <li class="page-item"><a class="page-link" @click = "getArticlesByPage('next')" >Next</a></li>
          </ul>

      </div>
      <div class="col-4">
        <Article v-if="selectedArticle" :article="selectedArticle"></Article>
      </div>
    </div>
    <Button class="btn-primary" @click="showForm">Dodaj Vest</Button><br>
    <br>
    <form ref="formNewArticle" style="visibility: hidden">
      <input ref="articleTitleInput" v-model="naslov"  type="text" required placeholder="Nalov vesti"/><br><br>
      <input ref="articleTagsInput" v-model="tags"  type="text" required placeholder="Tag1,tag2 ..."/><br><br>
      <input ref="articleTextInput" v-model="tekst"  type="text" required placeholder="Tekst vesti..."/><br><br>
      <select ref="select" v-model="selectedCategory" required>
        <option disabled value="">Please select one</option>
        <option v-for="category in categories" :key="category.id" @click="selectedCategory = category">{{ category.name }}</option>
      </select>
      <br><br>
      <button ref="btnAddArticle" @click="addNewArticle()" class="btn btn-primary m-1">Dodaj</button>
      <button ref="btnUpdateArticle" @click="updateArticle" class="btn btn-primary m-1" style="visibility: hidden">Izmeni</button>
    </form>
  </div>
</template>

<script>
import Article from "../components/Article";

export default {
  name: "Articles",
  components: {Article},
  data() {
    return {
      selectedArticle: null,
      articles: [],
      tekst: '',
      naslov: '',
      tags:'',
      categories:[],
      selectedCategory: '',
      pageNum: 0
    }
  },
  created() {
    this.$axios.get('/api/news/offset/0').then((response) => {
      this.articles = response.data;
    });
    this.$axios.get('/api/category').then((response) => {
      this.categories = response.data;
    });
  },
  methods:{

    getArticlesByPage(action){
      if(action == 'prev'){
        if(this.pageNum != 0)
          this.pageNum--;
      }else{
        this.pageNum++;
      }
      this.$axios.get('/api/news/offset/' + this.pageNum).then((response) => {
        this.articles = response.data;
      });
    },
    showForm() {
      this.$refs.formNewArticle.style.visibility = "visible"
      this.$refs.btnAdd.disabled = false
      this.$refs.articleTitleInput.value = ''
      this.$refs.articleTextInput.value = ''
      this.$refs.articleTagsInput.value = ''
      this.$refs.select.selectedIndex = 0
    },
    preUpdateArticle(article) {
      this.selectedArticle = article
      this.$refs.formNewArticle.style.visibility = "visible"
      this.$refs.articleTitleInput.value = article.title
      this.$refs.articleTextInput.value = article.text
      this.$refs.articleTagsInput.value = article.keyWords
      this.$refs.select.value = article.categoryName
      this.$refs.btnUpdateArticle.style.visibility = "visible"
      this.$refs.btnAddArticle.disabled = true
    },
    updateArticle(){
      const index = this.articles.indexOf(this.selectedArticle);
      this.$axios.put('/api/news', {
        id: this.selectedArticle.id,
        title: this.naslov,
        text: this.tekst,
        authorEmail: localStorage.getItem('authorEmail'),
        categoryName: this.selectedCategory,
        keyWords: Array.from(this.tags.split(",")),
      })
          .then((response) => {
            // this.categories[index] = response.data
            this.articles.splice(index, 1, response.data)
            console.log(response.data)
          })
          .catch(function (error) {
            console.log(error);
          });
      this.$refs.btnAddArticle.disabled = false
      this.$refs.btnUpdateArticle.style.visibility = "hidden"
      this.$refs.formNewArticle.style.visibility = "hidden"
    },
    deleteNews(article){
      console.log("delet se desio")
      this.$axios.delete('/api/news/' + article.id).then(() => {
        const index = this.articles.indexOf(article);
        if (index > -1) {
          this.articles.splice(index, 1);
        }
      });
    },
    addNewArticle() {
      this.$axios.post('/api/news', {
        title: this.naslov,
        text: this.tekst,
        authorEmail: localStorage.getItem('authorEmail'),
        categoryName: this.selectedCategory,
        keyWords: Array.from(this.tags.split(",")),
      })
          .then((response) => {
            this.articles.push(response.data)
          })
          .catch(function (error) {
            console.log(error);
          });
    }
  }
}
</script>
