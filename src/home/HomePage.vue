<template>
    <div>
        <div class="col-sm-12">
            <router-link to="/login" class="btn btn-primary">Logout</router-link>
        </div>
        <div class="col-sm-12">
            <apexcharts width="4000" height="600" :type="chartType" :options="options" :series="series">
            </apexcharts>
            <div class="btn-group-rounded col-xs-12">
                <button class="btn btn-success btn-xs" v-bind:class="{ selectedChartBtnClass: isActive}"  @click="changeChart('interestchart')">Area Chart View</button>
                <button class="btn btn-success btn-xs" v-bind:class="{ selectedChartBtnClass: !isActive}"  @click="changeChart('monthlychart')">Line Chart View</button>
            </div>
        </div>
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
            chartType:'area',
            selectedChartBtnClass:'',
            series: [{
                name: 'Search Volume',
                data: []
            }],
            SeriesPrice:[],
            XaxisTime:[],
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
                    tickAmount: 10,
                },
                xaxis: {
                    categories: [],
                    labels: {
                        show: false,
                    }
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
        this.loadChartData();
    },
    created () {
        this.getAllUsers();
    },
    methods: {
        ...mapActions('users', {
            getAllUsers: 'getAll',
            deleteUser: 'delete'
        }),
        loadChartData(){
            axios.get('https://www.gbm.com.mx/Mercados/ObtenerDatosGrafico?empresa=IPC', {header:{'Allow-Control-Allow-Origin': '*'}})
            .then(response => {
                for (var i=0;i<response.data.resultObj.length;i++)
                {
                    this.XaxisTime[i] = response.data.resultObj[i].Fecha;
                    this.SeriesPrice[i] = response.data.resultObj[i].Precio;
                }
                this.options = {
                        xaxis: {categories:this.XaxisTime.reverse()}
                    };
                this.series = [{
                    data: this.SeriesPrice.reverse()
                }];
            });
        },
        changeChart(select)
        {
            if(select === 'monthlychart')
            {
                this.isActive = false;
                this.chartType = 'bar';
                this.options = {
                    dataLabels:{enabled:false},
                    xaxis: {categories:this.XaxisTime.reverse(),
                        labels: {
                            show: false,
                            }
                    },
                    yaxis: {
                        opposite: false,
                        tickAmount: 10,
                    },
                };
            }
            else{
                this.isActive = true;
                this.chartType = 'area';
                this.options = {
                    dataLabels:{enabled:false},
                    xaxis: {categories:this.XaxisTime.reverse(),
                        labels: {
                            show: false,
                            }
                    },
                    yaxis: {
                        opposite: false,
                        tickAmount: 10,
                    },
                };
            }
        }
    }
};
</script>
<style>
    .btn-group-rounded .selectedChartBtnClass {
        color: white;
        background: #2da6e9;
        border-bottom: 2px solid #168ccd;
    }
   
</style>