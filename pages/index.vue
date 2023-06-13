<template>
  <cardLogin />
</template>

<script>
import cardLogin from '~/components/usuarios/card-login.vue'

export default {
  name: 'IndexPage',
  components: {
    cardLogin
  },
  layout: 'login'
}
</script>
