<template>
  <tablaAlumnos />
</template>

<script>
import tablaAlumnos from '~/components/principal/tabla-alumnos.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaAlumnos
  },
  layout: 'principal'
}
</script>
