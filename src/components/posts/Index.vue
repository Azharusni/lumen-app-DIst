<template>
    <div class="app">
        
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <div class="container-fluid">
            
            <div class="collapse navbar-collapse" id="navbarCollapse">
                <ul class="navbar-nav me-auto mb-2 mb-md-0">
                <li class="nav-item">
                    <router-link :to="{name:'posts'}" class="nav-link active" aria-current="page" href="#">Home</router-link>
                </li>
                
                </ul>
                <form class="d-flex">
                <div >
                            <router-link  :class="['btn btn-outline-warning right']" to="/logout"><i class="bi bi-box-arrow-right">Logout</i></router-link>
                </div>
                </form>
            </div>
            </div>
         </nav>
        <div class="container mt-5 ">
             
                <input type="text" class="form-control" style="width:300px;" v-model="searchTerm" placeholder="search here">
            <div class="row justify-content-center">
                <div class="col-md-12">  
                    <div class="card">
                        <div class="card-header">
                            NOTES
                        </div>
                        <div class="card-body">
                            <router-link :class="['btn btn-md btn-success mb-2']" to="/create">TAMBAH CATATAN</router-link>
                            <hr>

                            <div class="table-responsive mt-2">
                                <table class="table table-hover table-bordered">
                                    <thead>
                                    <tr>
                                        <th>TITLE</th>
                                        <th>KONTEN</th>
                                        <th>AKSI</th>
                                    </tr>
                                    </thead>
                                    <tbody>
                                    <tr v-for="post in filterSearch" :key="post.id">
                                        <td>{{ post.title }}</td>
                                        <td>{{ post.content }}</td>
                                        <td class="text-center">
                                            <router-link :to="{name: 'edit', params: { id: post.id }}" class="btn btn-sm btn-primary mr-2">EDIT</router-link>
                                            <button @click.prevent="PostDelete(post.id)" class="btn btn-sm btn-danger" >HAPUS</button>
                                        </td>
                                    </tr>
                                    </tbody>
                                      
                                </table>
                           
                            </div>
                            <div class="row">
                             <pagination id="page" v-if="pagination.last_page > 1" :pagination="pagination" :offset="5" @paginate="AllPost()"></pagination>
                             </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
    </div>
    
</template>


<script>
    import axios from 'axios'
    import pagination from './Pagination.vue';
    

    export default {
         
        components: {
                pagination
},
        mounted() {
             let token = localStorage.getItem('token');
                if(!token){
                  this.$router.push('/login')
                }            
            
        },

        data() {
            return {
                posts: [],
                searchTerm:'',
                pagination: {
            'current_page': 1
                 },
            }
        },
        
        computed:{
      filterSearch(){
                return this.posts.filter(post=>{
                return post.title.match(this.searchTerm)
                })
            }
            },
        
        methods: {
            AllPost(){
                let token = localStorage.getItem('token');
                let pagee = this.pagination.current_page;
            axios.get('http://18.141.176.150/post?api_token='+token+'&&page='+ pagee)
            .then(response => {
                this.posts = response.data.data.data;
                this.pagination = response.data.pagination;   
            })
            },

            PostDelete(id)
            {
                let token= localStorage.getItem('token')
                let del = confirm('Apakah anda yakin?')
                if(del==true){
                axios.delete(`http://18.141.176.150/post/${id}?api_token=`+token)
                    .then(response => {
                        this.posts.splice(this.posts.indexOf(id), 1);
                        this.post = response.data
                        window.alert("Data Berhasil dihapus");
                    })
                    .catch((error) => {
					if(error.response.status==422){
                        alert("Data gagal dihapus!")
					} 
                });
                }
                   
                
            },
            
        },

        created(){
           
            this.AllPost();
        }

    }
</script>


