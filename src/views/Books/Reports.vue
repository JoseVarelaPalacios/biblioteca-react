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


const genres = ref([]);
const books = ref([]);

const columns1 = ref([]);
const columns2 = ref([]);
const buttons1 = ref([]);
const buttons2 = ref([]);
const report = ref('1');
const types = ref([
    { id: '1', name: 'Books' },
    { id: '2', name: 'Genres' },
]);

onMounted(async () => {
        const g = await axios.get('/api/genres');
        genres.value = g.data;
        const b = await axios.get('/api/booksall');
        books.value = b.data;
    });

columns1.value = [
    { data: null, render:function (data, type, row, meta)
         {return (meta.row + 1)}},
    { data: 'name' },
    { data: 'isbn' },
    { data: 'pages' },
    { data: 'genre' },
];

columns2.value = [
    { data: null, render:function (data, type, row, meta)
        {return (meta.row + 1)}},
    { data: 'name' },
];

buttons1.value = [
    {
        title: 'Books',
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success',
    },
    {
        title: 'Books',
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger',
    },
    {
        title: 'Books',
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> Print',
        className: 'btn btn-dark',
    },
    {
        title: 'Books',
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> Copy',
        className: 'btn btn-secondary',
    },
];

buttons2.value = [
    {
        title: 'Genres',
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success',
    },
    {
        title: 'Genres',
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger',
    },
    {
        title: 'Genres',
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> Print',
        className: 'btn btn-dark',
    },
    {
        title: 'Genres',
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> Copy',
        className: 'btn btn-secondary',
    },
];
</script>

<template>
    <div class="row mb-5">
        <div class="col-md-8 offset-md-2">
            Report:
            <SelectInput id="rep" class="mt-1" v-model="report" :options="types" />
        </div>
    </div>
    <div class="row" v-if="report == '1'">
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable
                    :data="books"
                    :columns="columns1"
                    class="table table-striped"
                    :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons1 }"
                >
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>NAME</th>
                            <th>ISBN</th>
                            <th>PAGES</th>
                            <th>GENRE</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
    <div class="row" v-else>
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable
                    :data="genres"
                    :columns="columns2"
                    class="table table-striped"
                    :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons2 }"
                >
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>GENRES</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
</template>
