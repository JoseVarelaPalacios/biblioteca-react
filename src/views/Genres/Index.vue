<script setup>
import axios from 'axios';
import { ref,onMounted } from 'vue';
import { confirmation } from '../../functions';
import { useAuthStore } from '../../stores/auth';

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer '+ authStore.authToken;
onMounted(()=> { getGenres() });
const genres = ref([]);
const load = ref(false);
const getGenres = async () =>{
    await axios.get('/api/genres').then(
        response =>(genres.value = response.data))
        load.value = true;
}
const deleteGenre = (id,name)=>{
    confirmation(name,('/api/genres/'+id),'/genres');
}
</script>
<template>
    <div class="row">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <router-link :to="{ path:'create'}" class="btn btn-dark">
                    <i class="fa-solid fa-circle-plus"></i> Add
                </router-link>
            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-6 offset-md-3">
            <div class="card border border-white text-center" v-if="!load">
                <div class="card-body">
                    <img src="/loading.gif" class="img-fluid">
                </div>
            </div>
            <div class="table-responsive" v-else>
            <table class="table table-bordered table-hover">
                <thead>
                    <tr>
                        <th>#</th>
                        <th>GENRES</th>
                        <th></th>
                        <th></th>
                    </tr>
                </thead>
                <tbody class="table-group-divider">
                    <tr v-for="gen,i in genres " :key="gen.id">
                        <td>{{ (i+1) }}</td>
                        <td>{{ gen.name }}</td>
                        <td>
                            <router-link :to="{ path:'edit/'+gen.id}"
                            class="btn btn-warning">
                            <i class="fa-solid fa-edit"></i>
                            </router-link>
                        </td>
                        <td>
                            <button class="btn btn-danger"
                            @click="deleteGenre(gen.id,gen.name)">
                            <i class="fa-solid fa-trash"></i>
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        </div>
        
    </div>
</template>