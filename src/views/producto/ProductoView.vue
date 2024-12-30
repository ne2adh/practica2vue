<template>
    <div>
        <Modal v-model:modelValue="showModalNuevo">
            <ProductoNewView @on-register="onRegister($event)" />
        </Modal>
        <Modal v-model:modelValue="showModalEdit">
            <ProductoEditView @on-update="onUpdate($event)" :item="itemToEdit" />
        </Modal>
        <h1>Lista de Productos</h1>
        <button @click="showModalNuevo = true" class="btn btn-primary">Nuevo</button>
        
        <!-- Filtros y buscador -->
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
            <!-- Buscador general -->
            <div>
                <button @click="buscar()" class="btn btn-lith" style="float:right">Buscar</button>
                <input 
                    type="search" 
                    style="float:right" 
                    v-model="textToSearch" 
                    @search="buscar()" 
                    placeholder="Buscar por nombre">
            </div>

            <!-- Filtro por precio -->
            <div class="form-group" style="margin-left: 20px;">
                <label for="filtro-precio">Filtrar precio mayor a:</label>
                <input 
                    type="number" 
                    id="filtro-precio" 
                    v-model="precioFiltro" 
                    placeholder="Ej: 100" 
                    style="width: 150px; margin-right: 10px;" />
                <button @click="aplicarFiltroPorPrecio" class="btn btn-secondary">Aplicar</button>
            </div>
        </div>

        <!-- Tabla de productos -->
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Nombre</th>
                    <th>Precio</th>
                    <th>Stock</th>
                    <th>Categoría</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in productosFiltrados" :key="index">
                    <td>{{ 1 + index }}</td>
                    <td>{{ item.nombre }}</td>
                    <td>{{ item.precio }}</td>
                    <td>{{ item.stock }}</td>
                    <td>{{ item.categoria }}</td>
                    <td>
                        <button @click="edit(item)" class="btn btn-dark" style="margin-right: 15px;">Editar</button>
                        <button @click="Eliminar(item.id)" class="btn btn-danger">Eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import { mapState, mapGetters, mapActions } from 'vuex';
import Modal from '../../components/Modal.vue';
import ProductoNewView from './ProductoNewView.vue';
import ProductoEditView from './ProductoEditView.vue';

export default {
    name: 'Producto',
    data() {
        return {
            showModalNuevo: false,
            showModalEdit: false,
            itemToEdit: null,
            textToSearch: '',
            precioFiltro: 0, // Valor inicial del filtro
            itemList: [] // Lista completa de productos
        };
    },
    components: {
        Modal,
        ProductoNewView,
        ProductoEditView
    },
    methods: {
        ...mapActions(['increment']),
        getList() {
            const vm = this;
            this.axios.get(this.baseUrl + "/productos?_expand=categoria&q=" + this.textToSearch)
                .then(function (response) {
                    console.log(response);
                    vm.itemList = response.data;
                })
                .catch(function (error) {
                    console.error(error);
                });
        },
        aplicarFiltroPorPrecio() {
            console.log(`Filtrando productos con precio mayor a ${this.precioFiltro}`);
        },
        edit(item) {
            this.itemToEdit = Object.assign({}, item);
            this.showModalEdit = true;
        },
        Eliminar(id) {
            if (confirm("¿Está seguro de eliminar el registro?")) {
                const vm = this;
                this.axios.delete(this.baseUrl + "/productos/" + id)
                    .then(function (response) {
                        console.log(response);
                        vm.getList();
                        vm.$toast.show("Producto eliminado.", "danger");
                    })
                    .catch(function (error) {
                        console.error(error);
                    });
            }
        },
        buscar() {
            this.getList();
        },
        onRegister(event) {
            console.log("on register");
            this.getList();
            this.showModalNuevo = false;
            this.$toast.show('Producto registrado exitosamente', 'success');
        },
        onUpdate(event) {
            console.log("on update");
            this.getList();
            this.showModalEdit = false;
            this.itemToEdit = null;
            this.$toast.show('Producto editado exitosamente', 'info');
        }
    },
    computed: {
        ...mapState(['count']),
        ...mapGetters(['doubleCount', 'getBaseUrl']),
        baseUrl() {
            return this.getBaseUrl;
        },
        productosFiltrados() {
            // Filtrar productos por precio mayor al valor especificado
            return this.itemList.filter(item => item.precio > this.precioFiltro);
        }
    },
    mounted() {
        this.getList();
    },
    emits: []
};
</script>

<style></style>
