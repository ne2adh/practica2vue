<template>
  <div>
    <h1>Editar Producto</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="name">Nombre:</label>
        <input type="text" id="name" v-model="form.nombre" :class="{ 'is-invalid': errors.nombre }"
          placeholder="Ingrese el nombre del producto" />
        <div v-if="errors.nombre" class="invalid-feedback">{{ errors.nombre }}</div>
      </div>

      <div class="form-group">
        <label for="price">Precio:</label>
        <input type="number" id="price" v-model="form.precio" :class="{ 'is-invalid': errors.precio }"
          placeholder="Ingrese el precio" />
        <div v-if="errors.precio" class="invalid-feedback">{{ errors.precio }}</div>
      </div>

      <div class="form-group">
        <label for="stock">Stock:</label>
        <input type="number" id="stock" v-model="form.stock" :class="{ 'is-invalid': errors.stock }"
          placeholder="Ingrese el stock disponible" />
        <div v-if="errors.stock" class="invalid-feedback">{{ errors.stock }}</div>
      </div>

      <div class="form-group">
        <label for="category">Categoría:</label>
        <select id="category" v-model="form.categoriaId" :class="{ 'is-invalid': errors.categoriaId }">
          <option value="" disabled>Seleccione una categoría</option>
          <option v-for="categoria in categorias" :key="categoria.id" :value="categoria.id">
            {{ categoria.nombre }}
          </option>
        </select>
        <div v-if="errors.categoriaId" class="invalid-feedback">{{ errors.categoriaId }}</div>
      </div>

      <button type="submit" class="btn btn-primary">Actualizar</button>
    </form>
  </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex'

export default {
  name: 'ProductoEdit',
  data() {
    return {
      errors: {},
      categorias: []
    };
  },
  props: ['item'],
  methods: {
    ...mapActions(['increment']),
    validateForm() {
      this.errors = {};

      if (!this.form.nombre) {
        this.errors.nombre = 'El nombre del producto es obligatorio.';
      }

      if (!this.form.precio || this.form.precio <= 0) {
        this.errors.precio = 'El precio debe ser mayor a 0.';
      }

      if (!this.form.stock || this.form.stock < 0) {
        this.errors.stock = 'El stock no puede ser negativo.';
      }

      if (!this.form.categoriaId) {
        this.errors.categoriaId = 'Debe seleccionar una categoría.';
      }

      return Object.keys(this.errors).length === 0;
    },

    submitForm() {
      if (this.validateForm()) {
        this.save();
      }
    },

    save() {
      const vm = this;
      this.axios.patch(this.baseUrl + "/productos/" + this.form.id, this.form)
        .then(function (response) {
          if (response.status == 200) {
            vm.$emit('on-update', response.data);
          }
          console.log(response);
        })
        .catch(function (error) {
          console.error(error);
        });
    },

    getCategorias() {
      const vm = this;
      this.axios.get(this.baseUrl + "/categorias")
        .then(function (response) {
          vm.categorias = response.data;
        })
        .catch(function (error) {
          console.error(error);
        });
    }
  },
  computed: {
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl;
    },
    form() {
      return Object.assign({}, this.item);
    }
  },
  mounted() {
    this.getCategorias();
  }
}
</script>

<style scoped></style>
