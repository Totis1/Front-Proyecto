<template>
  <tablaCatalogo />
</template>

<script>
import tablaCatalogo from '~/components/referencias/tabla-catalogo.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaCatalogo
  },
  layout: 'principal'
}
</script>
