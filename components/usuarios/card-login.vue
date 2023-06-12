<template>
  <div>
    <v-row dense>
      <v-col cols="6">
        <v-card elevation="5" width="700" class="deep-purple-lighten-5">
          <v-card-title class="text-center">
            Iniciar Sesion
          </v-card-title>
          <v-card-text>
            <v-form ref="frmLogin">
              <v-text-field
                v-model="email"
                label="Email"
                placeholder="Escribe tu correo"
                :rules="validateEmail"
              />
              <v-text-field
                v-model="password"
                label="Contraseña"
                placeholder="Escribe tu Contraseña"
                type="password"
                :rules="validatePassword"
              />
            </v-form>
          </v-card-text>
          <v-card-actions>
            <v-btn block dark class="colorBtn" @click="ingresarSistema">
              <v-icon dense style="padding-right: 20px;">
                mdi-login
              </v-icon>
              Ingresar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col cols="6">
        <v-card class="deep-purple-lighten-5">
          <v-card-title>
            Usuario nuevo
          </v-card-title>
          <v-card-actions>
            <v-btn class="colorBtn" :disabled="true" block dark @click="registrarSistema">
              <v-icon dense style="padding-right: 20px;">
                mdi-account-credit-card-outline
              </v-icon>
              Registrar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
    <v-dialog
      v-model="dialogInsert"
      max-width="400"
      persistent
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-card-title>Nuevo Usuario</v-card-title>
        <v-card-text>
          <v-form ref="frmInsert">
            <v-text-field
              v-model="newalumno.name"
              label="Nombre:"
              :rules="validateName"
            />
            <v-text-field
              v-model="newalumno.lastname"
              label="Apellidos:"
              :rules="validateLastName"
            />
            <v-text-field
              v-model="newalumno.email"
              label="Correo:"
              :rules="validateEmail"
            />
            <v-text-field
              v-model="newalumno.password"
              label="Contraseña:"
              type="password"
              :rules="validatePassword"
            />
            <v-text-field
              v-model="newalumno.number"
              label="Numero:"
              :rules="validateNumber"
            />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="warning" @click="dialogInsert=false">
            Cancelar
          </v-btn>
          <v-btn color="green" @click="Insert()">
            Crear
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialog" width="600" transition="dialog-bottom-transition">
      <v-card>
        <v-card-title class="text-h5">
          Error
        </v-card-title>
        <v-spacer />
        <v-card-text>
          {{ mensaje }}
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogSuccessCreate" width="600" transition="dialog-bottom-transition" persistent>
      <v-card>
        <v-card-title class="green">
          Creacion Exitosa
        </v-card-title>
        <v-spacer />
        <v-card-text>
          Cuenta creada exitosamente, porfavor espere....
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data () {
    return {
      email: '',
      password: '',
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      validatePassword: [
        v => !v || v.length >= 8 || 'Password must have min 8 chars'
      ],
      validateName: [
        v => !v || v.length >= 3 || 'Password must have min 3 chars'
      ],
      validateLastName: [
        v => !v || v.length >= 3 || 'Password must have min 3 chars'
      ],
      validateNumber: [
        v => !v || v.length >= 10 || 'Password must have min 10 numbers'
      ],
      dialog: false,
      mensaje: '',
      dialogInsert: false,
      newalumno: {},
      dialogSuccessCreate: false
    }
  },
  methods: {
    async ingresarSistema () {
      if (this.email.length === 0 && this.password.length === 0) {
        alert('Algo anda mal')
        return
      }
      if (this.$refs.frmLogin.validate()) {
        const sendData = {
          email: this.email,
          password: this.password
        }
        await this.$auth.loginWith('local', {
          data: sendData
        }).then((res) => {
          if (!res.data.error) {
            this.$router.push('/dashboard')
          }
        }).catch((error) => {
          // eslint-disable-next-line no-console
          console.log(error)
          this.mensaje = 'Correo o Contraseña incorrecto/s'
          this.dialog = true
          setTimeout(() => {
            this.dialog = false
          }, 2000)
        })
      } else {
        alert('Algo anda Mal')
      }
    },
    registrarSistema () {
      this.dialogInsert = true
    },
    async Insert () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const alumnoCreate = {
        name: this.newalumno.name,
        lastname: this.newalumno.lastname,
        email: this.newalumno.email,
        password: this.newalumno.password,
        number: this.newalumno.number
      }
      await this.$axios.post('/insertar', alumnoCreate, config)
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log(res)
          this.dialogInsert = false
          this.dialogSuccessCreate = true
          setTimeout(() => {
            this.dialogSuccessCreate = false
          }, 2000)
          setTimeout(() => {
            const authfast = {
              email: alumnoCreate.email,
              password: alumnoCreate.password
            }
            this.$auth.loginWith('local', { auth: authfast })
              .then((res) => {
                if (!res.data.error) {
                  this.$router.push('/dashboard')
                }
              })
          }, 2000)
          this.$refs.frmInsert.reset()
        }).catch((err) => {
          // eslint-disable-next-line no-console
          console.error(err)
        })
    }
  }
}
</script>

<style scoped>
.colorBtn {
  background-color: green!important;
}
.v-dialog__container {
display: flex;
}
</style>
