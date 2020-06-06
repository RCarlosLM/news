<template>
    <div>
        
        <div class="container mt-5">
            <div class="card mb-3 name-tittle">
                <div class="card-header">
                    <h2>
                        Noticias
                    </h2>
                </div>
            </div>

            <div class="row">
                <div class="col-md-4" v-for="(news, i) in newArray" 
                    v-show="(pag - 1) * num_news <= i  && pag * num_news > i">
                    <div class="card mt-3 text-white bg-dark">
                        <div class="card-body">
                            <h5 class="card-title">
                                {{news.responseNews[i].name}}
                            </h5>
                            <h6 class="card-subtitle mb-2 text-muted">
                                <small class="badge badge-success font-italic text-white" style="width: 3rem;">AUTOR: </small>
                                {{news.random[0]}}
                            </h6>
                            <p class="card-text">{{news.responseNews[i].description}}</p>
                            <a class="card-link">
                                {{news.responseNews[i].category}}
                            </a>
                            <a class="card-link">
                                {{news.responseNews[i].language}}
                            </a>
                            <a :href="news.responseNews[i].url" target="_blank" class="card-link">url</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <nav aria-label="..." class="mt-3">
            <ul class="pagination justify-content-end">
                <li class="page-item">
                    <a  class="page-link" href="#" aria-disabled="true"
                        aria-label="Previous" v-show="pag != 1" @click.prevent="pag -= 1">
                        Anterior
                    </a>
                </li>
                <li class="page-item">
                    <a  class="page-link" href="#"
                        aria-label="Next" v-show="pag * num_news / newArray.length < 1" @click.prevent="pag += 1">Siguiente</a>
                </li>
            </ul>
        </nav>

    </div>
</template>

<script>

    import Vuetable from "vuetable-2";
    import VuetablePagination from "../utils/VuetablePaginationBootstrap4.vue";
    import VuetablePaginationInfo from 'vuetable-2/src/components/VuetablePaginationInfo'
    import CssConfig from "../utils/TableStyles";
    import VueEvents from 'vue-events'
    Vue.use(VueEvents)

    export default {
        props:[],
        components: {
            Vuetable,
            VuetablePagination,
            VuetablePaginationInfo,
        },
        data() {
            return {
                num_news: 10, // Numero de resultados por página
                pag: 1,
                newArray: []
            }
        },
        created() {

            this.getNews();
            
        },
        computed: {
        },
        methods: {

            getNews() {
                window.axios.defaults.headers.common = {
                    'Accept': 'application/json',
                    'Content-Type': 'application/json',
                };
                axios.get('https://newsapi.org/v2/sources?apiKey=093872c1f9f140638ccbfc4d3a390e2a', {  
                }).then(res => {
                    let responseNews = res.data.sources;
                    console.log(responseNews);
                    this.$snotify.success('Api conectada', 'Correcto', {
                        timeout: 4000,
                        showProgressBar: true,
                        closeOnClick: true,
                        pauseOnHover: true
                    });
                    axios.get('https://randomuser.me/api/?results=50', {  
                    }).then(res => {
                        let responseRandom = res.data.results;
                        let per = res.data.info;
                        this.pagina = per.page;
                        console.log('pagina', per);
                        for(let random of responseRandom){
                            for( random; random<=responseRandom; random++){
                                this.newArray.push({responseNews, random: [random.login.username]});
                            }
                        }
                        console.log('nuevo', this.newArray);
                    })
                }).catch(err => {

                    let response = err.response;

                    this.$snotify.error('Api desconectada', 'Error', {
                        timeout: 4000,
                        showProgressBar: true,
                        closeOnClick: true,
                        pauseOnHover: true
                    });


                })

            },

        },

        mounted() {
        },

    }
</script>