<script setup>
import axios from 'axios';
import { ref, onMounted,nextTick } from 'vue';
import { confirmation,sendRequest } from '../../functions';
import { useAuthStore } from '../../stores/auth';
import Modal from '../../components/Modal.vue';
import SelectInput from '../../components/SelectInput.vue';
import Paginate from 'vuejs-paginate-next';

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;

onMounted( ()=> { getGenres(), getBooks(1) });
const genres = ref([]);
const books = ref([]);
const load = ref(false);
const rows = ref(0);
const form = ref({
    name: '',
  isbn: '',
  pages: '',
  genre_id: '',
  
  
});
const title = ref('');
const nameInput = ref();
const operation = ref(1);
const id = ref('');
const close = ref([]);

const getGenres = async () => {
     await axios.get('/api/genres').then(
    response => (genres.value = response.data));
}
const getBooks = async (page) => {
     await axios.get('/api/books?page='+page).then(
    response => (
        books.value = response.data,
        rows.value = response.data.last_page
    ));
    load.value = true;
}
       
const deleteBook = (id, name) => { 
    confirmation(name, '/api/books/' + id, '/books');
}
const openModal = (op,name,isbn,pages,genre,book) =>{
    clear();
    setTimeout( ()=> nextTick( ()=> nameInput.value.focus()),600);
    operation.value = op;
    id.value = book;
    if(op == 1){
        title.value = 'Create book';
    }
    else{
        title.value='Update book';
        form.value.name = name;
        form.value.isbn = isbn;
        form.value.pages = pages;
        form.value.genre_id = genre;
        
    }
}
const clear = ()=>{
        form.value.name = '';
        form.value.isbn = '';
        form.value.pages = '';
        form.value.genre_id = '';
        
        
}




const save = async () =>{
    const formData = new FormData();
    formData.append('name', form.value.name);
    formData.append('isbn', form.value.isbn);
    formData.append('pages', form.value.pages);
    formData.append('genre_id', form.value.genre_id);
    

    let res;
    if(operation.value ==1){
        res = await sendRequest('POST',form.value,'/api/books', '');
        if (res == true) {
            clear();
            nextTick( ()=> nameInput.value.focus());
            getBooks(1);
        }
    }
    else{
        res = await sendRequest('PUT',form.value,('/api/books/'+id.value), '');
        if (res == true) {
            nextTick( ()=> close.value.click());
            getBooks(1);
        }
    }
}
</script>
<template>
 <div class="row">
    <div class="col-md-12">
        <div class="d-grid col-10 mx-auto">
            <button class="btn btn-dark" data-bs-toggle="modal"
            data-bs-target="#modal" @click="$event => openModal(1)">
                <i class="fa-solid fa-circle-plus"></i> ADD
            </button>
        </div>
    </div>
 </div>
<div class="row mt-3">
<div class="col-md-10 offset-md-1">
    <div class="card border border-white text-center" v-if="!load">
        <div class="card-body">
            <img src="/loading.gif" alt="Loading..." class="img-fluid">
        </div>
    </div>
    <div class="table-responsive"v-else>
<table class="table table-bordered table-hover">
<thead>
    <tr>
        <th >#</th>
        <th >NAME</th> 
        <th >ISBN</th>  
        <th >PAGES</th>  
        <th >GENRE</th>  
        
        <th ></th>  
        <th ></th>   
    </tr>
</thead>
<tbody class="table-group-divider">
 <tr v-for="bo,i in books.data" :key="bo.id">
    <td>{{i+1}}</td>
    <td>{{bo.name}}</td>
    <td>{{bo.isbn}}</td>
    <td>{{bo.pages}}</td>
    <td>{{bo.genre}}</td>
    


    <td>
        <button class="btn btn-warning" data-bs-toggle="modal"
            data-bs-target="#modal"
            @click="$event => openModal(2,bo.name,bo.isbn,bo.pages,bo.genre_id,bo.id)">
            <i class="fa-solid fa-edit"></i>
        </button>
    </td>
    <td>
        <button class="btn btn-danger"
        @click="$event => deleteBook(bo.id, bo.name)">
        <i class="fa-solid fa-trash"></i>
        </button>
    </td>
 </tr>
</tbody>
</table>
<Paginate :page-count="rows"
:click-handler="getBooks"
:prev-next="'Prev'" :next-text="Next"
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
                <i class="fa-solid fa-user"></i>
              </span>
              <input
                autofocus
                type="text"
                v-model="form.name"
                placeholder="Name"
                class="form-control"
                required
                ref="nameInput"
              />
            </div>
            <div class="input-group mb-3">
              <span class="input-group-text">
                <i class="fa-solid fa-barcode"></i>
              </span>
              <input
                required
                type="text"
                v-model="form.isbn"
                placeholder="ISBN"
                class="form-control"
              />
            </div>
            <div class="input-group mb-3">
              <span class="input-group-text">
                <i class="fa-solid fa-book-open"></i>
              </span>
              <input
                required
                type="text"
                v-model="form.pages"
                placeholder="Pages"
                class="form-control"
              />
            </div>
            <div class="input-group mb-3">
              <span class="input-group-text">
                <i class="fa-solid fa-tags"></i>
              </span>
              <SelectInput v-model="form.genre_id" :options="genres"
              class="form-select" required></SelectInput>
            </div>
            
            
            <div class="d-grid col-10 mx-auto">
                <button class="btn btn-success">
                  <i class="fa-solid fa-save"></i> SAVE
                </button>
            </div>
        </form>
    </div>
    <div class="modal-footer">
        <button class="btn btn-dark" ref="close"
        data-bs-dismiss="modal"> CLOSE </button>
    </div>
</Modal>

</template>
