<template>
  <div>
    <Article :article="article"></Article>
  </div>
</template>

<script>
import Article from "../components/Article";
export default {
  name: "SingleArticle",
  components: {Article},
  data() {
    return {
      article: {},
    }
  },
  created() {
    this.$axios.get(`/api/news/${this.$route.params.id}`).then((response) => {
      this.article = response.data;
    });
  }
}
</script>

<style scoped>

</style>
