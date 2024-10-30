<script setup>
import axios from 'axios';
import {ref,onMounted,nextTick} from 'vue';
import { confirmation,sendRequest } from '../../functions';
import { useAuthStore } from '../../stores/auth';
import Modal from '../../components/Modal.vue';
import SelectInput from '../../components/SelectInput.vue';
import Paginate from 'vuejs-paginate-next';

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer '+authStore.authToken;

onMounted( () =>{ getMarcas(), getEquipos(1) });
const marcas = ref([]);
const equipos = ref([]);
const load = ref(false);
const rows = ref(0);
const form = ref({
    ram:'',procesador:'',graficos:'',monitor:'',hd:'',descripcion:'',marcas_id:''
});
const title = ref('');
const nameInput = ref('');
const operation = ref(1);
const id = ref('');
const close = ref([]);
const getMarcas = async () =>{
    await axios.get('/api/marcas').then(
        response =>(
            marcas.value = response.data
        )
     )
     load.value = true;
}
const getEquipos = async (page) =>{
    await axios.get('/api/equipos?page='+page).then(
        response =>(
            equipos.value = response.data,
            rows.value = response.data.last_page
        )
     )
     load.value = true;
}

const deleteEquipo = (id,name) =>{
    confirmation(name,('/api/equipos/'+id),'/equipos');
}
const openModal = (op,ram,procesador,graficos,monitor,hd,descripcion,equipo) =>{
    clear();
    setTimeout( ()=> nextTick( ()=> nameInput.value.focus()),600);
    operation.value = op;
    id.value = equipo;
    if (op == 1) {
        title.value = 'Crear equipo';
    }else{
        title.value = 'actualizar equipo';
        form.value.ram = ram;
        form.value.procesador = procesador;
        form.value.graficos = graficos;
        form.value.monitor = monitor;
        form.value.hd = hd;
        form.value.descripcion = descripcion;
        form.value.marcas_id = marcas;
    }
}
const clear = ()=>{
    form.value.ram = '';
        form.value.procesador = '';
        form.value.graficos = '';
        form.value.monitor = '';
        form.value.hd = '';
        form.value.descripcion = '';
        form.value.marcas_id = '';
}
const save = async () =>{
    if (operation.value ==1) {
        res = await sendRequest('POST',form.value,'/api/equipos','');
        if (res == true) {
            clear();
            nextTick( ()=> nameInput.value.focus());
            getEquipos(1);
        }
    }
    else{
        res = await sendRequest('PUT',form.value,('/api/equipos'+id.value),'');
        if (res == true) {
            nextTick( ()=> close.value.click());
            getEquipos(1);
        }
    }
}
</script>
<template>
    <div class="row">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <button class="btn btn-dark" data-bs-toggle="modal"
        data-bs-target="#modal" @click="openModal(1)">
    <i class="fa-solid fa-circle-plus"></i>Add
</button>

            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-10 offset-md-3">
            <div class="card border border-white text-center" v-if="!load">
                <div class="card-body">
                    <img src="/loading-gif-steiness.gif" class="img-fluid">
                </div>
            </div>
            <div class="table-responsive" v-else>
            <table class="tble table-bordered table-hover">
                <thead><tr><th>#</th><th>ram</th><th>procesador</th><th>graficos</th><th>hd</th><th>descripcion</th><th>marca</th><th></th><th></th></tr></thead>
                <tbody class="table-group-divider">
                    <tr v-for="eq,i in equipos.data" :key="eq.id">
                        <td>{{ (i+1) }}</td>
                        <td>{{ (eq.ram) }}</td>
                        <td>{{ (eq.procesador) }}</td>
                        <td>{{ (eq.graficos) }}</td>
                        <td>{{ (eq.monitor) }}</td>
                        <td>{{ (eq.hd) }}</td>
                        <td>{{ (eq.descripcion) }}</td>
                        <td>{{ (eq.marca) }}</td>
                        <td>
                            <button class="btn btn-warning" data-bs-toggle="modal"
                            data-bs-target="#modal" @click="$event => openModal(2,eq.ram
                                ,eq.procesador,eq.graficos,eq.monitor,eq.hd,eq.descripcion,eq.marcas_id,eq.id)">
                                <i class="fa-solid fa-edit"></i>

                            </button>
                        </td>
                        <td>
                            <button class="btn btn-danger" @click="$event=>deleteEquipo(eq.id,eq.name)">
                                <i class="fa-solid fa-trash"></i>
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
            <Paginate :page-count="rows"
            :click-handler="getEquipos"
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
                            <span class="input-group-text"><i class="fa-solid fa-user"></i>
                            </span>
                            <input autofocus type="text" v-model="form.name"
                        placeholder="Ram" class="form-control" required ref="nameInput">
                </div>
                <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fa-solid fa-user"></i>
                            </span>
                            <input required type="text" v-model="form.name"
                        placeholder="Procesador" class="form-control">
                </div>
                <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fa-solid fa-user"></i>
                            </span>
                            <input required type="text" v-model="form.name"
                        placeholder="graficos" class="form-control">
                </div>
                <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fa-solid fa-user"></i>
                            </span>
                            <input required type="text" v-model="form.name"
                        placeholder="monitor" class="form-control">
                </div>
                <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fa-solid fa-user"></i>
                            </span>
                            <input required type="text" v-model="form.name"
                        placeholder="hd" class="form-control">
                </div>
                <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fa-solid fa-user"></i>
                            </span>
                            <input required type="text" v-model="form.name"
                        placeholder="descripcion" class="form-control">
                </div>
                <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fa-solid fa-building"></i>
                            </span>
                            <SelectInput v-model="form.marcas_id" :options="marcas"
                            class="form-select" required></SelectInput>
                </div>
                <div class="d-grid col-10 mx-auto">
                    <button class="btn btn-dark">
                        <i class="fa-solid fa-save"></i> Save</button>
                </div>
            </form>
        </div>
        <div class="modal-footer">
            <button class="btn btn-dark" ref="close"
            data-bs-dismiss="modal">
                close
            </button>
        </div>
    </Modal>
</template>