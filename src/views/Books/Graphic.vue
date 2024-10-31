<script setup>
import {ref, onMounted} from 'vue';
import { useAuthStore } from '../../stores/auth';
import {Bar,Pie} from 'vue-chartjs';
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, 
    LinearScale, ArcElement} from 'chart.js';
    ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale, ArcElement);

    const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;

const genres = ref([]);
const books = ref([]);
const chartData = ref([]);
const chartOptions = ref([]);
const colors = ref([]);
const loaded = ref(false);
const random = () =>{
    return Math.floor(Math.random() * 256);
}
onMounted( async () => {
    const info = await axios.get('/api/booksbygenre');
    info.data.map((row) => (
        genres.value.push(row.name),
        books.value.push(row.count),
        colors.value.push("rgb("+random()+","+random()+","+random()+")")
    ));
    chartOptions.value = { responsive:true}
    chartData.value = {
        labels: genres.value,
        datasets:[
            {label: 'Books', data:books.value, backgroundColor:colors}
        ]
    };
    loaded.value = true;
})

</script>
<template>
    <div class="row">
        <div class="col-md-8 offset-md-2" >
            <Bar v-if="loaded" :data="chartData" :options="chartOptions"></Bar>

        </div>
    </div>
</template>