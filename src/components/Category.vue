<template>
  <div>
    <h2>
      name: {{ category.name }}
    </h2>

    <h5>description:</h5>
    <p>
      {{ category.description }} <br><br>
    </p>
  </div>
</template>

<script>

export default {
  name: "Category",
  props: {
    category: {
      id: Number,
      name: String,
      description: String
    }
  },
}
</script>

<style scoped>

</style>
