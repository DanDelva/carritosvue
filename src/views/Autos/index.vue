<script setup>
import axios from 'axios';
import { ref, onMounted, nextTick } from 'vue';
import { confirmation } from '../../function';
import { useAuthStore } from '../../stores/auth';
import Modal from '../../components/Modal.vue';
import SelectInput from '../../components/SelectInput.vue';
import Paginate from 'vuejs-paginate-next';
import Swal from 'sweetalert2'; // Importar SweetAlert2 para las notificaciones

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;

onMounted(() => { getMarcas(); getAutos(1); });

const marcas = ref([]);
const autos = ref([]);
const load = ref(false);
const rows = ref(0);
const form = ref({
    id: '',
    modelo: '',
    color: '',
    precio: '',
    transmision: '',
    submarca: '',
    marca_id: ''
});

const title = ref('');
const marcaInput = ref('');
const operation = ref(1);
const id = ref('');
const close = ref([]);

const getMarcas = async () => {
    await axios.get('/api/marcas').then(
        response => (marcas.value = response.data)
    );
};

const getAutos = async (page) => {
    await axios.get('/api/autos?page=' + page).then(
        response => {
            autos.value = response.data.data; // Ajuste para acceder a los datos
            rows.value = response.data.last_page;
        }
    );
    load.value = true;
};

const deleteAutos = (id, marca) => {
    confirmation(marca, ('/api/autos/' + id), '/autos');
};

const openModal = (op, id = '', modelo = '', color = '', precio = '', transmision = '', submarca = '', marca_id = '') => {
    clear();
    setTimeout(() => nextTick(() => marcaInput.value.focus()), 600);
    operation.value = op;
    if (op === 1) {
        title.value = 'Create Auto';
    } else {
        title.value = 'Edit Auto';
        form.value.id = id;
        form.value.modelo = modelo;
        form.value.color = color;
        form.value.precio = precio;
        form.value.transmision = transmision;
        form.value.submarca = submarca;
        form.value.marca_id = marca_id;
    }
};

const clear = () => {
    form.value.id = '';
    form.value.modelo = '';
    form.value.color = '';
    form.value.precio = '';
    form.value.transmision = '';
    form.value.submarca = '';
    form.value.marca_id = '';
};

const showNotification = (message, icon) => {
    Swal.fire({
        title: message,
        icon: icon,
        confirmButtonText: 'OK',
        timer: 2000,
        timerProgressBar: true,
        showCloseButton: true
    });
};

const save = async () => {
    const formData = {
        modelo: form.value.modelo,
        color: form.value.color,
        precio: form.value.precio,
        transmision: form.value.transmision,
        submarca: form.value.submarca,
        marca_id: form.value.marca_id,
    };

    let res;
    if (operation.value === 1) {
        res = await axios.post('/api/autos', formData);
        if (res.status === 200) {
            clear();
            nextTick(() => marcaInput.value.focus());
            getAutos(1);
            showNotification('Auto creado', 'success');
        }
    } else {
        res = await axios.put('/api/autos/' + form.value.id, formData);
        if (res.status === 200) {
            nextTick(() => close.value.click());
            getAutos(1);
            showNotification('Auto editado', 'success');
        }
    }
};
</script>

<template>
    <div class="row">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <button class="btn btn-dark" data-bs-toggle="modal" data-bs-target="#modal" @click="openModal(1)">
                    <i class="fa-solid fa-circle-plus"></i> Add
                </button>
            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-10 offset-md-1">
            <div class="card border border-white text-center" v-if="!load">
                <div class="card-body">
                    <img src="/carros gif.gif" alt="Loading" class="img-fluid">
                </div>
            </div>
            <div class="table-responsive" v-else>
                <table class="table table-bordered table-hover">
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>ID</th>
                            <th>MODELO</th>
                            <th>COLOR</th>
                            <th>PRECIO</th>
                            <th>TRANSMISION</th>
                            <th>SUBMARCA</th>
                            <th>MARCA_ID</th>
                            <th></th>
                            <th></th>
                        </tr>
                    </thead>
                    <tbody class="table-group-divider">
                        <tr v-for="(aut, i) in autos" :key="aut.id">
                            <td>{{ (i+1) }}</td>
                            <td>{{ aut.id }}</td>
                            <td>{{ aut.modelo }}</td>
                            <td>{{ aut.color }}</td>
                            <td>{{ aut.precio }}</td>
                            <td>{{ aut.transmision }}</td>
                            <td>{{ aut.submarca }}</td>
                            <td>{{ aut.marca_id }}</td>
                            <td>
                                <button class="btn btn-warning" data-bs-toggle="modal" data-bs-target="#modal" 
                                @click="openModal(2, aut.id, aut.modelo, aut.color, aut.precio, aut.transmision, aut.submarca, aut.marca_id)">
                                    <i class="fa-solid fa-edit"></i>
                                </button>
                            </td>
                            <td>
                                <button class="btn btn-danger" @click="deleteAutos(aut.id, aut.marca)">
                                    <i class="fa-solid fa-trash"></i>
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
                <Paginate :page-count="rows" :click-handler="getAutos"
                :prev-text="'Prev'" :next-text="'Next'" 
                :container-class="'pagination'">
                </Paginate>
            </div>
        </div>
    </div>
    <Modal :id="'modal'" :title="title">
        <div class="modal-body">
            <form @submit.prevent="save">
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-palette"></i>
                    </span>
                    <input type="text" v-model="form.modelo" placeholder="Modelo" class="form-control" required ref="marcaInput">
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-palette"></i>
                    </span>
                    <input type="text" v-model="form.color" placeholder="Color" class="form-control" required>
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-dollar-sign"></i>
                    </span>
                    <input type="number" v-model="form.precio" placeholder="Precio" class="form-control" required min="0" step="0.01">
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-car"></i>
                    </span>
                    <input type="text" v-model="form.transmision" placeholder="Transmision" class="form-control" required>
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-car"></i>
                    </span>
                    <input type="text" v-model="form.submarca" placeholder="Submarca" class="form-control" required>
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-building"></i>
                    </span>
                    <SelectInput v-model="form.marca_id" :options="marcas" placeholder="Marca" required />
                </div>
                <button type="submit" class="btn btn-success">
                    <i class="fa-solid fa-save"></i> Guardar
                </button>
            </form>
        </div>
        <div class="modal-footer">
            <button class=" btn-dark" ref="close"
            data-bs-dismiss="modal">Close</button>
        </div>
    </Modal>
</template>
