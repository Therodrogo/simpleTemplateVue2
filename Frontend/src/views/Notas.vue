<template>
    <div>
        <form v-if="!editar">
            <h3 class="text-center">Agregar nueva Nota</h3>
            <input type="text" placeholder="Ingrese un Nombre" class="form-control my-2" v-model="nota.nombre">
            <input type="text" placeholder="Ingrese una descripcion" class="form-control my-2" v-model="nota.descripcion">
            <b-button @click="agregarNota()" class="btn-sm btn-block btn-success" type="submit">Agregar</b-button>
        </form>
        <form v-if="editar">
            <h3 class="text-center">Editar Nota</h3>
            <input type="text" placeholder="Ingrese un Nombre" class="form-control my-2" v-model="notaEditar.nombre">
            <input type="text" placeholder="Ingrese una descripcion" class="form-control my-2" v-model="notaEditar.descripcion">
            <b-button @click="editarNota(notaEditar)" class="btn-sm btn-block btn-warning" type="submit">Editar</b-button>
            <b-button @click="cancelarEdicion()" class="btn-sm btn-block btn-danger" type="submit">Cancelar</b-button>
        </form>
        <table class="table">
           
        <thead>
            <tr>
            <th scope="col">#</th>
            <th scope="col">Nombre</th>
            <th scope="col">Descripción</th>
            <th scope="col">Fecha</th>
            <th scope="col">Acciones</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(item, index) in notas" :key="index">
            <th scope="row">{{item._id}}</th>
            <td>{{item.nombre}}</td>
            <td>{{item.descripcion}}</td>
            <td>{{item.date}}</td>
            <td>
                <b-button class="btn-warning btn-sm mx-2" @click="activarEdicion(item._id)">Actualizar</b-button>
                <b-button class="btn-danger btn-sm mx-2" @click="eliminarNota(item._id)">Eliminar</b-button>
            </td>
            </tr>
        </tbody>
        </table>
    </div>
</template>

<script>
import  Swal from "sweetalert2"
export default {
    data() {
        return {
            notas: [],
            nota: {
                nombre:"",
                descripcion:""
            },
            agregar: true,
            editar:false,
            notaEditar:{}
        };
    },
    created(){
        this.listarNotas();
        
        
    },
    methods:{
        listarNotas(){
            this.axios.get('/nota')
            .then((res) => {
                // console.log(response.data)
                this.notas = res.data;
            })
            .catch((e)=>{
                console.log(e.response);
            })
        },
        agregarNota(){
            this.axios.post('/nueva-nota',this.nota)
            .then(res =>{
                this.notas.push(res.data)

                this.notas.nombre =""
                this.notas.descripcion=""
                Swal.fire({
                    position: 'top',
                    icon: 'success',
                    title: 'Nueva nota agregada',
                    toast: true,
                    timer: 1500
                })

            })
            .catch((e)=>{
                console.log(e.response);
                Swal.fire({
                    position: 'top',
                    icon: 'error',
                    title: 'Falta informcion',
                    toast: true,
                    timer: 1500
                })
            })

        },
        eliminarNota(id){
            this.axios.delete("/nota/"+id)
            .then(res =>{
                const index = this.notas.findIndex(item => item._id == res.data._id)
                this.notas.splice(index,1)
                Swal.fire({
                    position: 'top',
                    icon: 'success',
                    title: 'Se borro con exito.',
                    toast: true,
                    timer: 1500
                })

            })
            .catch(e =>{
                console.log(e.response);
                Swal.fire({
                    position: 'top',
                    icon: 'error',
                    title: 'Elemento no existe.',
                    toast: true,
                    timer: 1500
                })
            })
        },
        activarEdicion(id){
            this.editar=true
            this.axios.get("/nota/"+id)

            .then(res =>{
                this.notaEditar= res.data
            })
            .catch(e =>{
                console.log(e.response);
            })
        },
        cancelarEdicion(){
            this.editar = false
        },
        editarNota(item){
            //Se Edita en el Backend
            this.axios.put("/nota/"+item._id,item)
            //Se Actualiza en el Frontend
            .then(res =>{
                const index = this.notas.findIndex(item => item._id == res.data._id)
                this.notas[index].nombre = res.data.nombre
                this.notas[index].descripcion = res.data.descripcion
                this.editar = false

                Swal.fire({
                    position: 'top',
                    icon: 'success',
                    title: 'Edicion con exito.',
                    toast: true,
                    timer: 1500
                })
            })
            .catch(e =>{
                console.log(e.response);
                Swal.fire({
                    position: 'top',
                    icon: 'error',
                    title: 'Elemento no existe.',
                    toast: true,
                    timer: 1500
                })
            })

        }
    }
   
};
</script>

<style scoped>

</style>