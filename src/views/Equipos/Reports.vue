<script setup>
import { ref, onMounted } from 'vue';
import { useAuthStore } from '../../stores/auth';
import SelectInput from '../../components/SelectInput.vue';
import DataTable from 'datatables.net-vue3';
import 'datatables.net-dt/css/dataTables.dataTables.css';

import ButtonsHtml5 from 'datatables.net-buttons/js/buttons.html5';
import 'datatables.net-buttons/js/buttons.print';
import 'datatables.net-responsive-dt';
import 'datatables.net-responsive-dt/css/responsive.dataTables.min.css';
import JsZip from 'jszip';
import pdfmake from 'pdfmake/build/pdfmake';
import * as pdfFonts from 'pdfmake/build/vfs_fonts';

pdfmake.vfs = pdfFonts.pdfMake ? pdfFonts.pdfMake.vfs : pdfmake.vfs;
window.JSZip = JsZip;
DataTable.use(ButtonsHtml5);

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;

const marcas = ref([]);
const equipos = ref([]);
const columns1 = ref([
    { data: null, render: (data, type, row, meta) => (meta.row + 1) },
    { data: 'ram' },
    { data: 'procesador' },
    { data: 'graficos' },
    { data: 'monitor' },
    { data: 'hd' },
    { data: 'descripcion' },
]);

const columns2 = ref([
    { data: null, render: (data, type, row, meta) => (meta.row + 1) },
    { data: 'marca' },
]);

const buttons1 = ref([]);
const buttons2 = ref([]); 

const report = ref('1');
const types = ref([
    { id: '1', name: 'Equipos' },
    { id: '2', name: 'Marcas' },
]);

onMounted(async () => {
    try {
        const responseMarcas = await axios.get('/api/marcas');
        marcas.value = responseMarcas.data;
        console.log('Marcas:', marcas.value);

        const responseEquipos = await axios.get('/api/equiposall');
        equipos.value = responseEquipos.data;
        console.log('Equipos:', equipos.value);
    } catch (error) {
        console.error('Error loading data:', error);
    }
});
buttons1.value = [
    {
        title: 'Equipos',
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success',
    },
    {
        title: 'Equipos',
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger',
    },
    {
        title: 'Equipos',
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> Print',
        className: 'btn btn-dark',
    },
    {
        title: 'Equipos',
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> Copy',
        className: 'btn btn-secondary',
    },
];

buttons2.value = [
    {
        title: 'Marcas',
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success',
    },
    {
        title: 'Marcas',
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger',
    },
    {
        title: 'Marcas',
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> Print',
        className: 'btn btn-dark',
    },
    {
        title: 'Marcas',
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> Copy',
        className: 'btn btn-secondary',
    },
];
</script>

<template>
    <div class="row mb-5">
        <div class="col-md-8 offset-md-2">
            Reports:
            <SelectInput id="rep" class="mt-1" v-model="report" :options="types"></SelectInput>
        </div>
    </div>

    <div class="row" v-if="report == '1'">
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable 
                    :data="equipos" 
                    :columns="columns1"
                    class="table table-striped"
                    :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons1 }">
                </DataTable>
            </div>
        </div>
    </div>

    <div class="row" v-else>
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable 
                    :data="marcas" 
                    :columns="columns2"
                    class="table table-striped"
                    :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons2 }">
                </DataTable>
            </div> 
        </div>
    </div>
</template>
