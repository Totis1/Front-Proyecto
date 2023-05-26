<template>
  <v-app>
    <v-row class="centrado">
      <Nuxt />
    </v-row>
  </v-app>
</template>

<script>
export default {
  name: 'Login'
}
</script>

<style scoped>
.centrado{
  display: flex;
  align-items: center;
  justify-content: center;
  background-color:#3F51B5;
}
</style>
