<template>
  <div>
    <b-form @submit="onSubmit" @reset="onReset" v-if="show">

      <b-form-group id="input-group-1" label="Nombre:" label-for="input-1">
        <b-form-input id="input-1" v-model="form.nombre" placeholder="Enter Name" required>
        </b-form-input>
      </b-form-group>

      <b-form-group id="input-group-2" label="Tipo:" label-for="input-2">
        <b-form-input id="input-2" v-model="form.tipo" placeholder="Enter type" required></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-3" label="Customizable:" label-for="input-3">
        <b-form-select id="input-3" v-model="form.personalizable" :options="types" required></b-form-select>
      </b-form-group>

      <b-form-group id="input-group-4" label="Precio:" label-for="input-4">
        <b-form-input id="input-4" v-model="form.precio" placeholder="Enter price" @keypress="onlyNumber" required></b-form-input>
      </b-form-group>

      <b-form-group id="input-group-5" label="Foto:" label-for="input-5">
        <b-form-file id="input-5" type="file" v-model="form.picByte" @change="obtenerImagen($event)" accept="image/*" placeholder="Pick a photo" ></b-form-file>
      </b-form-group>

      <b-button type="submit" variant="primary">Submit</b-button>
      <b-button type="reset" variant="danger">Reset</b-button>
    </b-form>
    <b-card class="mt-3" header="Form Data Result">
      <pre class="m-0">{{ form }}</pre>
    </b-card>
  </div>
</template>
<script>
import Producto from '../model/Producto';
export default {
  mounted() {
    if (localStorage.getItem('token')!=null) {
      this.$axios.defaults.headers.common['Authorization'] = 'Bearer ' + localStorage.getItem('token');
    }else{
      this.$router.push('/log_in')
    }
    if(localStorage.getItem('authorities')!='restaurante'){
      alert("Debe iniciar sesión como restaurante!");
      this.$router.push('/log_in')
    }
  },
  data() {
    return {
      form: {
        nombre: '',
        tipo: null,
        customizable: false,
        precio: null,
        picByte: null,
        checked: []
      },
      types: [{ text: 'Select One', value: null }, 'Si', 'No'],
      show: true
    }
  },
  methods: {
    obtenerImagen(e){
                this.form.picByte= e.target.files[0];
                console.log(this.form.picByte);
    },
    onSubmit(event) {
      event.preventDefault()
      alert(JSON.stringify(this.form))
      console.log(this.form.personalizable);
      if(this.form.personalizable=='Si'){
        this.form.personalizable=true
      }else{
        this.form.personalizable=false
      }
      const producto = new Producto(this.form.nombre, this.form.tipo, this.form.personalizable, this.form.precio);
      console.log(producto)
      if(this.form.picByte!=null){
          const uploadData = new FormData();
          uploadData.append('imageFile', this.form.picByte, this.form.picByte.name);
          this.$axios.$post("http://localhost:8080/productos/upload", uploadData )
          .then(res => {
            console.log(res);
            if (res.mensaje == "200") {
              console.log('subi la imagen')
              this.message2 = 'Image uploaded successfully';
              this.$axios.$post("http://localhost:8080/productos/newproduct", producto)
              .then(response => console.log(response))
              .catch(response => console.log(response),error => {
              this.errorMessage = error.message;
              console.error("There was an error!", error);
              });
            } else {
              console.log('f');
              console.log(res.status);
              this.message2 = 'Image not uploaded successfully';
            }
          }).catch((promise) => {
            console.log(promise)
            this.handleError(promise)}
            );
      }else{
            this.$axios.$post("http://localhost:8080/productos/newproduct", producto)
              .then(response => console.log(response))
              .catch(response => console.log(response),error => {
              this.errorMessage = error.message;
              console.error("There was an error!", error);
              });
      }
    },
    onReset(event) {
      event.preventDefault()
      // Reset our form values
      this.form.nombre = ''
      this.form.tipo = null
      this.form.customizable = false
      this.form.precio= null
      this.form.picByte= null
      this.form.checked = []
      // Trick to reset/clear native browser form validation state
      this.show = false
      this.$nextTick(() => {
        this.show = true
      })
    },
    onlyNumber($event) {
      //console.log($event.keyCode); //keyCodes value
      let keyCode = ($event.keyCode ? $event.keyCode : $event.which);
      if ((keyCode < 48 || keyCode > 57) && keyCode !== 46) { // 46 is dot
        $event.preventDefault();
      }
    }
  },
 /* computed: {
    validation() {
      //return this.form.unidadMedida.length >= 1 && this.form.unidadMedida.length <=5
    }
  }*/
}
</script>
