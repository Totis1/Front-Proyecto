<template>
  <v-row class="principal" style="margin:10px; padding: 10px;">
    <v-col cols="12">
      <v-row class="principal">
        Usuarios del Sistema
      </v-row>
      <v-row class="principal">
        <v-data-table
          :headers="headers"
          :items="usuarios"
          style="width: 100%"
        >
          <template #[`item.acciones`]="{ item }">
            <v-row>
              <v-col cols="6">
                <v-btn icon color="orange" @click="dialogU(item)">
                  <v-icon>mdi-account-edit-outline</v-icon>
                </v-btn>
              </v-col>
              <v-col cols="6">
                <v-btn icon color="red" @click="dialogDelete(item)">
                  <v-icon>mdi-account-remove-outline</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </template>
        </v-data-table>
      </v-row>
    </v-col>
    <v-dialog
      v-model="dialogBorrado"
      max-width="290"
      persistent
    >
      <v-card>
        <v-card-title class="text-h5">
          Borrado de Alumno
        </v-card-title>
        <v-card-text>
          ¿Seguro que deseas borrar el Alumno?
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="green darken-1"
            text
            @click="dialogBorrado = false"
          >
            Cancelar
          </v-btn>
          <v-btn
            color="red darken-4"
            text
            @click="borrar()"
          >
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="dialogUpdate"
      max-width="400"
      persistent
    >
      <v-card>
        <v-card-title>Actualizacion de datos</v-card-title>
        <v-card-text>
          <v-form red="frmUpdate">
            <v-text-field
              v-model="alumno.name"
              label="Nombre:"
              :rules="validateName"
            />
            <v-text-field
              v-model="alumno.lastname"
              label="Apellidos:"
              :rules="validateLastName"
            />
            <v-text-field
              v-model="alumno.number"
              label="Numero:"
              :rules="validateNumber"
            />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="warning" @click="dialogUpdate=false">
            Cancelar
          </v-btn>
          <v-btn color="green" @click="update()">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
export default {
  data () {
    return {
      usuarios: [],
      headers: [
        {
          text: 'Nombre',
          sortable: true,
          value: 'name'
        },
        {
          text: 'Apellidos',
          sortable: true,
          value: 'lastname'
        },
        {
          text: 'Correo',
          sortable: true,
          value: 'email'
        },
        {
          text: 'Numero',
          sortable: true,
          value: 'number'
        },
        {
          text: 'Acciones',
          sortable: true,
          value: 'acciones'
        }
      ],
      dialogBorrado: false,
      emailBorrar: '',
      dialogUpdate: false,
      alumno: {},
      validateName: [
        v => !v || v.length >= 3 || 'Name must have min 3 chars'
      ],
      validateLastName: [
        v => !v || v.length >= 3 || 'LastName must have min 3 chars'
      ],
      validateNumber: [
        v => !v || v.length >= 10 || 'Number must have min 10 numbers'
      ]
    }
  },
  created () {
    this.loadUsers()
  },
  methods: {
    async loadUsers () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/traertodo', config)
        .then((res) => {
          this.usuarios = res.data.data
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.log(error)
        })
    },
    dialogDelete (item) {
      this.emailBorrar = item.email
      this.dialogBorrado = true
    },
    async borrar () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const sendData = {
        email: this.emailBorrar
      }
      await this.$axios.post('/eliminar', sendData, config)
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log(res)
          if (!res.data.error) {
            this.dialogBorrado = false
            this.loadUsers()
          } else {
            alert(res.data.data)
          }
        })
        .catch((e) => {
          // eslint-disable-next-line no-console
          console.log(e)
        })
    },
    dialogU (item) {
      this.alumno = item
      this.dialogUpdate = true
    },
    async update () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const alumnoUpdate = {
        name: this.alumno.name,
        lastname: this.alumno.lastname,
        email: this.alumno.email,
        number: this.alumno.number
      }
      await this.$axios.post('/actualizar', alumnoUpdate, config)
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log(res)
          this.dialogUpdate = false
          this.loadUsers()
        }).catch((e) => {
          // eslint-disable-next-line no-console
          console.log(e)
        })
    }
  }
}
</script>

<style scoped>
.principal{
  width: 100%;
}
</style>
