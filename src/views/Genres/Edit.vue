<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { sendRequest } from '../../functions';
import { useAuthStore } from '../../stores/auth';

const route = useRoute();
const authStore = useAuthStore()
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;

const form = ref({ id:'', name: '' });
const id = ref(route.params.id);
onMounted(() => {getGenre()});
const getGenre = () =>{
  axios.get('api/genres/'+id.value).then(
    response => (form.value.name = response.data.data.name)
  );
}

const save = () => {
    sendRequest('PUT', form.value,('/api/genres/'+id.value), '/genres');
}
</script>

<template>
  <div class="row mt-5">
    <div class="col-md-4 offset-md-4">
      <div class="card border border-success">
        <div class="card-header bg-success border border-success">EDIT GENRE</div>
        <div class="card-body">
          <form @submit.prevent="save">
            <div class="input-group mb-3">
              <span class="input-group-text">
                <i class="fa-solid fa-book"></i>
              </span>
              <input
                autofocus
                type="text"
                v-model="form.name"
                placeholder="Genre"
                class="form-control"
                required
                
              />
            </div>
            <div class="d-grid col-10 mx-auto">
              <button class="btn btn-dark">
                <i class="fa-solid fa-save"> SAVE</i>
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
