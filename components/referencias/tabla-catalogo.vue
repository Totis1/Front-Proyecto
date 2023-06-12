<template>
  <div>
    <br>
    <v-row class="principal">
      <v-btn right dark class="colorBtn" @click="registrarModelo()">
        <v-icon dense style="padding-right: 10px;">
          mdi-briefcase-variant-outline
        </v-icon>
        Registrar Modelo
      </v-btn>
    </v-row>
    <v-col cols="12">
      <br> <br>
      <v-row class="principal">
        Modelos en el Sistema
      </v-row>
      <br>
      <v-container>
        <v-row justify="center">
          <v-col v-for="(item, index) in modelos" :key="item.id" cols="12" md="6">
            <v-card @mouseenter="showImage(index)" @mouseleave="hideImage">
              <v-img :src="item.Imagen" height="200px" />
              <v-card-title>{{ item.id_Modelo }}</v-card-title>
              <v-card-text>{{ item.Nombre }}</v-card-text>
              <v-card-actions>
                <v-btn text>
                  Eliminar
                </v-btn>
              </v-card-actions>
              <div v-if="showingImageIndex === index" class="image-container">
                <img :src="item.Imagen" alt="Imagen">
              </div>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-col>
    <v-dialog
      v-model="dialogInsertM"
      max-width="400"
      persistent
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-card-title>Nuevo Modelo</v-card-title>
        <v-card-text>
          <v-form ref="frmInsertM">
            <v-text-field
              v-model="newmodelo.id_Modelo"
              label="Identificador del Modelo:"
            />
            <v-text-field
              v-model="newmodelo.Nombre"
              label="Nombre:"
            />
            <v-select
              v-model="newmodelo.Categoria"
              :items="categoria"
              label="Seleccione una Categoria"
            />
            <v-text-field
              v-model="newmodelo.GrModelo"
              label="Gramos del Modelo:"
              type="number"
            />
            <v-text-field
              v-model="newmodelo.TiempoImp"
              label="Tiempo de Impresion en Horas:"
              type="number"
            />
            <v-file-input
              v-model="inputFile"
              label="Selecciona una Imagen"
              accept="image/png, image/jpeg"
              variant="filled"
              prepend-icon="mdi-camera"
              @change="clickimagen"
            />
          </v-form>
        </v-card-text>
        <img class="preview" :src="datoimagen">
        <v-card-actions>
          <v-btn color="warning" @click="dialogInsertM=false">
            Cancelar
          </v-btn>
          <v-btn color="green" @click="InsertarModelo()">
            Crear
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
// import {getStorage, ref, getDownloadURL, uploadBytes } from ('firebase/storage')
export default {
  data () {
    return {
      rules: [
        value => !value || value.size < 8000000 || 'Avatar size should be less than 8 MB!'
      ],
      inputFile: null,
      modelos: [],
      showingImageIndex: null,
      datoimagen: null,
      imageUrl: null,
      newmodelo: {},
      modelo: {
        id_Modelo: '',
        Nombre: '',
        Categoria: '',
        GrModelo: 0,
        TiempoImp: 0,
        Imagen: ''
      },
      dialogInsertM: false,
      categoria: ['Prototipo', 'Protesis', 'Arte', 'Moda', 'Gadgets', 'Modelos', 'Herramientas', 'Juguetes']
    }
  },
  created () {
    this.loadModelos()
  },
  methods: {
    showImage (index) {
      this.showingImageIndex = index
    },
    hideImage () {
      this.showingImageIndex = null
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
    async InsertarModelo () {
      const formData = new FormData()
      formData.append('Imagen', this.newmodelo.Imagen)
      // eslint-disable-next-line no-console
      await this.$axios.post('/insertarimgmodelo', formData)
        .then((res) => {
          // eslint-disable-next-line no-console
          console.log('bandera')
          this.imageUrl = res.data.imageUrl
          // eslint-disable-next-line no-console
          console.log(this.imageUrl)
          const ModeloCreate = {
            id_Modelo: this.newmodelo.id_Modelo,
            Nombre: this.newmodelo.Nombre,
            Categoria: this.newmodelo.Categoria,
            GrModelo: this.newmodelo.GrModelo,
            TiempoImp: this.newmodelo.TiempoImp,
            Imagen: this.imageUrl
          }
          this.$axios.post('/insertarmodelo', ModeloCreate)
            .then((res) => {
              // eslint-disable-next-line no-console
              console.log(res)
              this.datoimagen = null
              this.inputFile = null
              this.dialogInsertM = false
              this.loadModelos()
            }).catch((err) => {
              // eslint-disable-next-line no-console
              console.error(err)
            })
        }).catch((err) => {
          // eslint-disable-next-line no-console
          console.error(err)
        })
    },
    clickimagen (e) {
      this.newmodelo.Imagen = e
      // eslint-disable-next-line no-console
      console.log(this.newmodelo.Imagen)
      const reader = new FileReader()
      reader.readAsDataURL(this.newmodelo.Imagen)
      reader.onload = (e) => {
        this.datoimagen = e.target.result
      }
    },
    registrarModelo () {
      this.dialogInsertM = true
    }
  }
}
</script>

<style scoped>
.colorBtn {
  background-color: green!important;
}
.image-container{
  text-align: center;
}
.preview{
  width: 300px;
}
.principal{
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
