<template>
  <div>
    <h2>
      name: {{ user.firstName }}
    </h2>

    <h5>lastName:</h5>
    <p>
      {{ user.lastName }} <br><br>
    </p>
  </div>
</template>

<script>

export default {
  name: "User",
  props: {
    user: {
      id: Number,
      email: String,
      firstName: String,
      lastName: String,
      type: String,
      active: Boolean
    }
  },
}
</script>

<style scoped>

</style>
