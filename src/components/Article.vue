<template>
  <div>
    <h2>
      Naslov: {{ article.title }}
    </h2>

    <h5>Tekst:</h5>
    <p>
      {{ article.text }} <br><br>
    </p>
  </div>
</template>

<script>

export default {
  name: "Article",
  props: {
    article: {
      title: String,
      text: String,
      authorEmail: String,
      categoryName: String,
      keyWords: []
    }
  },
}
</script>

<style scoped>

</style>
