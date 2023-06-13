<template>
  <div>
    <v-row class="principal" style="margin:10px; padding: 10px;">
      <v-col cols="12">
        <v-row class="principal">
          Ventas en el Sistema
        </v-row>
        <v-row>
          <v-btn right dark class="colorBtn" @click="registrarventa()">
            <v-icon dense style="padding-right: 10px;">
              mdi-cash-register
            </v-icon>
            Registrar Venta
          </v-btn>
        </v-row>
        <v-row class="principal">
          <v-data-table
            :headers="headers"
            :items="ventas"
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
            Borrar la Venta
          </v-card-title>
          <v-card-text>
            ¿Seguro que deseas borrar la venta?
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
      />
    </v-row>
    <v-dialog
      v-model="dialogInsertV"
      max-width="400"
      persistent
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-card-title>Nueva Venta</v-card-title>
        <v-card-text>
          <v-form ref="frmInsertV">
            <v-text-field
              v-model="newventa.Nombre"
              label="Nombre:"
              :rules="validateName"
            />
            <v-btn class="colorBtn" dark @click="newDialog">
              Selecciona Modelo
            </v-btn> <br> <br>
            <v-text-field
              v-model="newventa.Numero"
              type="number"
              :min="1"
              :max="15"
              label="Vendidos"
            />
            <v-text-field
              v-model="newventa.Categoria"
              label="Categoria:"
              outlined
              disabled
            />
            <v-text-field
              v-model="newventa.Modelo"
              label="ID del Modelo:"
              outlined
              disabled
            />
            <v-text-field
              v-model="newventa.NombreM"
              label="Nombre del Modelo:"
              outlined
              disabled
            />
            <v-text-field
              v-model="newventa.GrModelo"
              label="Gramos del Modelo:"
              outlined
              disabled
              type="number"
            />
            <v-text-field
              v-model="newventa.TiempoImp"
              label="Tiempo de Impresion (Horas):"
              type="number"
              outlined
              disabled
            />
            <v-select
              v-model="newventa.Tmaterial"
              :items="tipos"
              label="Tipo de material:"
              @change="material"
            />
            <v-text-field
              v-model="newventa.Seguro"
              label="Seguro %:"
              type="number"
            />
            <v-text-field
              v-model="newventa.Insumos"
              label="Insumos:"
              type="number"
              outlined
              disabled
            />
            <v-btn class="colorBtn" :disabled="!activador" dark @click="calcular()">
              Calcular Precio Total
            </v-btn> <br> <br>
            <v-text-field
              v-model="newventa.Ptotal"
              label="Precio Total:"
              type="number"
              outlined
              disabled
            />
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              :return-value.sync="date"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template #activator="{ on, attrs }">
                <v-text-field
                  v-model="date"
                  label="Fecha de Venta"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                />
              </template>
              <v-date-picker
                v-model="date"
                no-title
                scrollable
              >
                <v-spacer />
                <v-btn
                  text
                  color="primary"
                  @click="menu = false"
                >
                  Cancel
                </v-btn>
                <v-btn
                  text
                  color="primary"
                  @click="$refs.menu.save(date)"
                >
                  OK
                </v-btn>
              </v-date-picker>
            </v-menu>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="warning" @click="dialogInsertV=false">
            Cancelar
          </v-btn>
          <v-btn color="green" :disabled="activador" @click="InsertarVenta()">
            Crear
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="dialogUpdateV"
      max-width="400"
      persistent
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-card-title>Actualizar Venta</v-card-title>
        <v-card-text>
          <v-form ref="frmUpdateV">
            <v-text-field
              v-model="venta.Nombre"
              label="Nombre:"
              :rules="validateName"
            />
            <v-btn class="colorBtn" dark @click="newDialog">
              Selecciona Modelo
            </v-btn> <br> <br>
            <v-text-field
              v-model="venta.Numero"
              type="number"
              :min="1"
              :max="15"
              label="Vendidos"
            />
            <v-text-field
              v-model="venta.Categoria"
              label="Categoria:"
              outlined
              disabled
            />
            <v-text-field
              v-model="venta.Modelo"
              label="ID del Modelo:"
              outlined
              disabled
            />
            <v-text-field
              v-model="venta.NombreM"
              label="Nombre del Modelo:"
              outlined
              disabled
            />
            <v-text-field
              v-model="venta.GrModelo"
              label="Gramos del Modelo:"
              outlined
              disabled
              type="number"
            />
            <v-text-field
              v-model="venta.TiempoImp"
              label="Tiempo de Impresion (Horas):"
              type="number"
              outlined
              disabled
            />
            <v-select
              v-model="venta.Tmaterial"
              :items="tipos"
              label="Tipo de material:"
              @change="material2"
            />
            <v-text-field
              v-model="venta.Seguro"
              label="Seguro %:"
              type="number"
            />
            <v-text-field
              v-model="venta.Insumos"
              label="Insumos:"
              type="number"
              outlined
              disabled
            />
            <v-btn class="colorBtn" :disabled="!activador" dark @click="calcular2()">
              Calcular Precio Total
            </v-btn> <br> <br>
            <v-text-field
              v-model="venta.Ptotal"
              label="Precio Total:"
              type="number"
              outlined
              disabled
            />
            <v-menu
              ref="menu2"
              v-model="menu2"
              :close-on-content-click="false"
              :return-value.sync="date2"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template #activator="{ on, attrs }">
                <v-text-field
                  v-model="date2"
                  label="Fecha de Venta"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                />
              </template>
              <v-date-picker
                v-model="date2"
                no-title
                scrollable
              >
                <v-spacer />
                <v-btn
                  text
                  color="primary"
                  @click="menu2 = false"
                >
                  Cancel
                </v-btn>
                <v-btn
                  text
                  color="primary"
                  @click="$refs.menu2.save(date2)"
                >
                  OK
                </v-btn>
              </v-date-picker>
            </v-menu>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="warning" @click="dialogUpdateV=false">
            Cancelar
          </v-btn>
          <v-btn color="green" :disabled="activador" @click="update()">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <div>
      <v-dialog v-model="dialogOpen">
        <v-card v-for="card in modelos" :key="card.id">
          <v-img
            :src="card.Imagen"
            height="125"
            contain
          />
          <v-card-title>{{ card.id_Modelo }}</v-card-title>
          <v-card-text>{{ card.Nombre }}</v-card-text>
          <v-card-actions>
            <v-btn color="primary" text @click="closeNewDialog(card)">
              Seleccionar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      ventas: [],
      headers: [
        {
          text: 'Piezas',
          sortable: true,
          value: 'Numero'
        },
        {
          text: 'Nombre',
          sortable: true,
          value: 'Nombre'
        },
        {
          text: 'Categoria',
          sortable: true,
          value: 'Categoria'
        },
        {
          text: 'Modelo',
          sortable: true,
          value: 'Modelo'
        },
        {
          text: 'Nombre del Modelo',
          sortable: true,
          value: 'NombreM'
        },
        {
          text: 'Material Utilizado (g)',
          sortable: true,
          value: 'GrModelo'
        },
        {
          text: 'Tiempo de Impresion',
          sortable: true,
          value: 'TiempoImp'
        },
        {
          text: 'Tipo de Material',
          sortable: true,
          value: 'Tmaterial'
        },
        {
          text: 'Seguro',
          sortable: true,
          value: 'Seguro'
        },
        {
          text: 'Precio de Insumos',
          sortable: true,
          value: 'Insumos'
        },
        {
          text: 'Precio Total',
          sortable: true,
          value: 'Ptotal'
        },
        {
          text: 'Fecha',
          sortable: true,
          value: 'Fecha'
        },
        {
          text: 'Acciones',
          sortable: true,
          value: 'acciones'
        }
      ],
      date: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      date2: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      menu: false,
      menu2: false,
      dialogOpen: false,
      dialogUpdateV: false,
      dialogBorrado: false,
      activador: true,
      emailBorrar: '',
      valort: 0,
      bandera: '',
      dialogInsertV: false,
      dialogUpdate: false,
      tipos: [
        { text: 'PLA', value: 400 },
        { text: 'PETG', value: 420 },
        { text: 'ABS', value: 480 },
        { text: 'TPU', value: 620 }
      ],
      newventa: {
        Numero: 1
      },
      venta: {},
      deleteado: {},
      modelo: {
        id_Modelo: '',
        Nombre: '',
        GrModelo: 0,
        TiempoImp: 0,
        Imagen: ''
      },
      modelos: [],
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
    this.loadVentas()
    this.loadModelos()
  },
  methods: {
    dialogDelete (item) {
      this.deleteado = item.Nombre
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
        Nombre: this.deleteado
      }
      await this.$axios.post('/eliminarventa', sendData, config)
        .then((res) => {
          if (!res.data.error) {
            this.dialogBorrado = false
            this.loadVentas()
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
      this.venta = item
      this.dialogUpdateV = true
    },
    async update () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const ventaUpdate = {
        Numero: this.venta.Numero,
        Nombre: this.venta.Nombre,
        Categoria: this.venta.Categoria,
        Modelo: this.venta.Modelo,
        NombreM: this.venta.NombreM,
        GrModelo: this.venta.GrModelo,
        TiempoImp: this.venta.TiempoImp,
        Tmaterial: this.venta.Tmaterial,
        Seguro: this.venta.Seguro,
        Insumos: this.venta.Insumos,
        Ptotal: this.venta.Ptotal,
        Fecha: this.date
      }
      await this.$axios.post('/actualizarventa', ventaUpdate, config)
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log(res)
          this.dialogUpdateV = false
          this.loadModelos()
        }).catch((e) => {
          // eslint-disable-next-line no-console
          console.log(e)
        })
    },
    calcular () {
      this.activador = false
      this.newventa.Ptotal = (((this.newventa.GrModelo / 1000) * this.valort) +
      (0.85 * this.newventa.TiempoImp) + (30) + (15) + ((((this.newventa.GrModelo / 1000) * this.valort) +
      (0.85 * this.newventa.TiempoImp) + (30) + (15)) * (this.newventa.Seguro / 100))) * this.newventa.Numero
    },
    calcular2 () {
      this.activador = false
      this.venta.Ptotal = (((this.venta.GrModelo / 1000) * this.valort) +
      (0.85 * this.venta.TiempoImp) + (30) + (15) + ((((this.venta.GrModelo / 1000) * this.valort) +
      (0.85 * this.venta.TiempoImp) + (30) + (15)) * (this.venta.Seguro / 100))) * this.venta.Numero
    },
    material () {
      const Opcion = this.tipos.find(tipo => tipo.value === this.newventa.Tmaterial)
      if (Opcion) {
        this.valort = Opcion.value
        this.bandera = Opcion.text
      }
    },
    material2 () {
      const Opcion = this.tipos.find(tipo => tipo.value === this.newventa.Tmaterial)
      if (Opcion) {
        this.valort = Opcion.value
        this.bandera = Opcion.text
      }
    },
    async loadModelos () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/traermodelos', config)
        .then((res) => {
          this.modelos = res.data.data
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.log(error)
        })
    },
    async InsertarVenta () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const ventaCreate = {
        Numero: this.newventa.Numero,
        Nombre: this.newventa.Nombre,
        Categoria: this.newventa.Categoria,
        Modelo: this.newventa.Modelo,
        NombreM: this.newventa.NombreM,
        GrModelo: this.newventa.GrModelo,
        TiempoImp: this.newventa.TiempoImp,
        Tmaterial: this.bandera,
        Seguro: this.newventa.Seguro,
        Insumos: this.newventa.Insumos,
        Ptotal: this.newventa.Ptotal,
        Fecha: this.date
      }
      await this.$axios.post('/insertarventas', ventaCreate, config)
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log(res)
          this.loadVentas()
          this.dialogInsertV = false
          this.$refs.frmInsertV.reset()
        }).catch((err) => {
          // eslint-disable-next-line no-console
          console.error(err)
        })
    },
    async loadVentas () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/traerventas', config)
        .then((res) => {
          this.ventas = res.data.data
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.log(error)
        })
    },
    registrarventa () {
      this.dialogInsertV = true
    },
    newDialog () {
      this.dialogOpen = true
    },
    closeNewDialog (card) {
      this.dialogOpen = false
      // eslint-disable-next-line no-console
      console.log(card)
      this.newventa.Categoria = card.Categoria
      this.newventa.Modelo = card.id_Modelo
      this.newventa.NombreM = card.Nombre
      this.newventa.GrModelo = card.GrModelo
      this.newventa.TiempoImp = card.TiempoImp
      this.newventa.Insumos = 0.85 * this.newventa.TiempoImp

      this.venta.Categoria = card.Categoria
      this.venta.Modelo = card.id_Modelo
      this.venta.NombreM = card.Nombre
      this.venta.GrModelo = card.GrModelo
      this.venta.TiempoImp = card.TiempoImp
      this.venta.Insumos = 0.85 * this.venta.TiempoImp
    }
  }
}
</script>

<style scoped>
.colorBtn {
  background-color: green!important;
}
.principal{
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
