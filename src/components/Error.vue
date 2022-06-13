<template>
  <div>
    <h2>
      Error
    </h2>

    <h5>Tekst:</h5>
    <p>
      {{ errText }}
    </p>
  </div>
</template>

<script>

export default {
  name: "Error",
  props: {
      errText: String
  },
}
</script>

<style scoped>

</style>
