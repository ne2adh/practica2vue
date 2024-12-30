<template>
  <div>
    <h1>Registrar Categoría</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="nombre">Nombre de la categoría:</label>
        <input type="text" id="nombre" v-model="form.nombre" :class="{ 'is-invalid': errors.nombre }"
          placeholder="Ingrese el nombre" />
        <div v-if="errors.nombre" class="invalid-feedback">{{ errors.nombre }}</div>
      </div>

      <div class="form-group">
        <label for="descripcion">Descripción:</label>
        <textarea id="descripcion" v-model="form.descripcion" :class="{ 'is-invalid': errors.descripcion }"
          placeholder="Ingrese la descripción"></textarea>
        <div v-if="errors.descripcion" class="invalid-feedback">{{ errors.descripcion }}</div>
      </div>

      <button type="submit" class="btn btn-primary">Registrar</button>
    </form>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex';

export default {
  name: 'CategoriaNew',
  data() {
    return {
      form: {
        nombre: '',
        descripcion: '',
      },
      errors: {},
    };
  },
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.nombre) {
        this.errors.nombre = 'El nombre es obligatorio.';
      }

      if (!this.form.descripcion) {
        this.errors.descripcion = 'La descripción es obligatoria.';
      }

      return Object.keys(this.errors).length === 0;
    },
    submitForm() {
      if (this.validateForm()) {
        // Enviar los datos al servidor
        this.save();
        // Reiniciar el formulario
        this.form = {
          nombre: '',
          descripcion: '',
        };
      }
    },
    save() {
      const vm = this;
      this.axios
        .post(this.baseUrl + '/categorias', this.form)
        .then(function (response) {
          if (response.status === 201) {
            vm.$emit('on-register', response.data);
          }
        })
        .catch(function (error) {
          console.error(error);
        });
    },
  },
  computed: {
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl;
    },
  },
};
</script>

<style scoped></style>
