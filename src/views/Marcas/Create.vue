<script setup>
import { ref, nextTick } from 'vue';
import { sendRequest } from '../../function';
import { useAuthStore } from '../../stores/auth';
const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;
const form = ref({ marca: '' });
const marcaInput = ref('');
const save = () => {
    sendRequest('POST', form.value, '/api/marcas', '');
    form.value.marca = '';
    nextTick(() => marcaInput.value.focus());
};
</script>
<template>
   <div class="row mt-5">
        <div class="col-md-4 offset-md-4">
            <div class="card border border-success">
                <div class="card-header bg-success border border-success"></div>
                <div class="card-body">
                    <form @submit.prevent="save">
                        <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-building"></i>
                            </span>
                            <input autofocus type="text" v-model="form.marca" 
                            placeholder="Marca" class="form-control" required ref="marcaInput">
                        </div>
                        <div class="d-grid mx-auto">
                            <button class="btn btn-outline-dark">
                                <i class="fa-solid fa-save"></i>Save
                            </button>
                        </div> 
                     </form>
                </div>
            </div>
        </div>
    </div> 
</template>