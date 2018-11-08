<template>
    <div>
        
        <apexcharts width="600" height="300" :type="chartType" :options="options" :series="series">
        </apexcharts>

<div class="btn-group-rounded float-right col-xs-12">
            <button class="btn btn-default btn-xs" v-bind:class="{ selectedChartBtnClass: !isActive}"  @click="changeChart('interestchart')">Interest Over Time</button>
            <button class="btn btn-default btn-xs" v-bind:class="{ selectedChartBtnClass: isActive}"  @click="changeChart('monthlychart')">Monthly Search Volumes</button>
        </div>
        <ul v-if="users.items">
            <li v-for="user in users.items" :key="user.id">
                {{user.firstName + ' ' + user.lastName}}
                <span v-if="user.deleting"><em> - Deleting...</em></span>
                <span v-else-if="user.deleteError" class="text-danger"> - ERROR: {{user.deleteError}}</span>
                <span v-else> - <a @click="deleteUser(user.id)" class="text-danger">Delete</a></span>
            </li>
        </ul>
        <p>
            <router-link to="/login">Logout</router-link>
        </p>
    </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import VueApexCharts from 'vue-apexcharts'
export default {
    components: {
      apexcharts: VueApexCharts,
    },
     data() {
        return {
            isActive:true,
            chartType:'bar',
            selectedChartBtnClass:'',
            series: [{
                name: 'Search Volume',
                data: []
            }],
            monthlySeries:[],
            options: {
                dataLabels: {
                    enabled: false
                },
                stroke: {
                    curve: 'smooth'
                },
                chart: {
                    id: 'vuechart-example',
                    sparkline: {
                        enabled: false,
                    }
                },
                yaxis: {
                    seriesName: 'Search Volume',
                    opposite: false,
                    tickAmount: 2,
                },
                xaxis: {
                    categories: [],
                    
                },
            },
        }
     },
    computed: {
        ...mapState({
            account: state => state.account,
            users: state => state.users.all
        })
    },
    mounted () {
        axios.get('https://www.gbm.com.mx/Mercados/ObtenerDatosGrafico?empresa=IPC')
        .then(response => {
        console.log(response);
        });
    },
    created () {
        this.getAllUsers();
    },
    methods: {
        ...mapActions('users', {
            getAllUsers: 'getAll',
            deleteUser: 'delete'
        }),
        changeChart(select)
        {
            if(select === 'monthlychart')
            {
                this.isActive = true;
                this.chartType = 'bar';
                this.options = {
                    dataLabels:{enabled:false},
                    xaxis: {categories:this.monthlySeries.reverse()},
                    yaxis: {
                        opposite: false,
                        tickAmount: 2,
                    },
                };
            }
            else{
                this.isActive = false;
                this.chartType = 'area';
                this.options = {
                    dataLabels:{enabled:false},
                    xaxis: {categories:this.monthlySeries.reverse()},
                    yaxis: {
                        opposite: false,
                        tickAmount: 2,
                    },
                };
            }
        }
    }
};
</script>