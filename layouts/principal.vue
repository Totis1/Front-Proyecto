<template>
  <v-app>
    <v-navigation-drawer
      class="deep-purple accent-4"
      dark
      permanent
      absolute
      app
    >
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">
            Barra de Navegacion
          </v-list-item-title>
          <v-list-item-subtitle>
            Acciones
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>
      <v-divider />
      <v-list
        dense
        nav
      >
        <v-list-item
          v-for="item in items"
          :key="item.title"
          link
          :to="item.path"
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <template #append>
        <div class="pa-2" @click="logout()">
          <v-btn block>
            Cerrar Sesion
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>
    <v-main>
      <Nuxt />
    </v-main>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      items: [
        { title: 'Ventas', icon: 'mdi-cash', path: '/ventas' },
        { title: 'Catalogo', icon: 'mdi-image', path: '/catalogo' },
        { title: 'Usuarios', icon: 'mdi-account-circle', path: '/dashboard' },
        { title: 'Graficos', icon: 'mdi-finance', path: '/graficos' }
      ],
      right: null
    }
  },
  methods: {
    async logout () {
      await this.$auth.logout(/* .... */)
    }
  }
}
</script>
