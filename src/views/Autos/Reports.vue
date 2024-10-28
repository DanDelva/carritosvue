<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';
import { useAuthStore } from '../../stores/auth';
import SelectInput from '../../components/SelectInput.vue';
import DataTable from 'datatables.net-vue3';
import 'datatables.net-dt/css/dataTables.dataTables.min.css'; // Ajusta la ruta si es necesario
import ButtonsHtml5 from 'datatables.net-buttons/js/buttons.html5';
import 'datatables.net-buttons/js/buttons.print';
import 'datatables.net-responsive-dt';
import 'datatables.net-responsive-dt/css/responsive.dataTables.min.css';
import JsZip from 'jszip';
import pdfmake from 'pdfmake/build/pdfmake';
import * as pdfFonts from 'pdfmake/build/vfs_fonts';

// Configuración de pdfmake y JSZip
pdfmake.vfs = pdfFonts.pdfMake ? pdfFonts.pdfMake.vfs : pdfmake.vfs;
window.JSZip = JsZip;
DataTable.use(ButtonsHtml5);

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;

const marcas = ref([]);
const autos = ref([]);
const columns1 = ref([]);
const columns2 = ref([]);
const buttons1 = ref([]);
const buttons2 = ref([]);
const report = ref('1');
const types = ref([
    { 'id': '1', 'marca': 'Autos' },
    { 'id': '2', 'marca': 'Marcas' }
]);

onMounted(async () => {
    try {
        const d = await axios.get('/api/marcas');
        marcas.value = d.data;

        const e = await axios.get('/api/autosall');
        autos.value = e.data;
    } catch (error) {
        console.error('Error fetching data:', error);
    }
});

columns1.value = [
    { data: null, render: function (data, type, row, meta) { return (meta.row + 1); }},
    { data: 'id' },
    { data: 'modelo' },
    { data: 'color' },
    { data: 'precio' },
    { data: 'transmision' },
    { data: 'submarca' },
    { data: 'marca_id' },
    { data: 'imagen' }
];

columns2.value = [
    { data: null, render: function (data, type, row, meta) { return (meta.row + 1); }},
    { data: 'marca' }
];

buttons1.value = [
    {
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success'
    },
    {
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger'
    },
    {
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> PRINT',
        className: 'btn btn-dark'
    },
    {
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> COPY',
        className: 'btn btn-secondary'
    }
];

buttons2.value = [
    {
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success'
    },
    {
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger'
    },
    {
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> PRINT',
        className: 'btn btn-dark'
    },
    {
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> COPY',
        className: 'btn btn-secondary'
    }
];
</script>

<template>
    <div class="row mb-5">
        <div class="col-md-8 offset-md-2">
            Report:
            <SelectInput id="rep" class="mt-1" v-model="report" :options="types">
            </SelectInput>
        </div>
    </div>
    <div class="row" v-if="report == '1'">
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable :data="autos" :columns="columns1"
                        class="table table-striped"
                        :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons1 }">
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
                            <th>IMAGEN</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
    <div class="row" v-else>
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable :data="marcas" :columns="columns2"
                           class="table table-striped"
                           :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons2 }">
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>MARCAS</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
</template>
