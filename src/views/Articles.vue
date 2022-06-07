<template>
  <div class="articles">
    <h1 class="mt-4">Articles</h1>

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
          <tr v-for="article in articles" :key="article.id" @click="selectedArticle = article">
            <th scope="row">{{ article.id }}</th>
            <td>{{ article.title }}</td>
            <td>{{ article.text }}</td>
          </tr>
          </tbody>
        </table>
      </div>
      <div class="col-6">
        <Article v-if="selectedArticle" :article="selectedArticle"></Article>
      </div>
    </div>
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
      articles: []
    }
  },
  created() {
    this.$axios.get('/api/news/offset/0').then((response) => {
      this.articles = response.data;
      console.log(this.articles)
    });
  },
}
</script>
