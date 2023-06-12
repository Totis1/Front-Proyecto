<template>
  <tablaVenta />
</template>

<script>
import tablaVenta from '~/components/ventas/tabla-ventas.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaVenta
  },
  layout: 'principal'
}
</script>
