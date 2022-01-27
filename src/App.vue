<template>
  <div id="app">
    <div class="anadir">
        <button v-if="!anadiendo" v-on:click="anade" class="anadir_tarea">Añadir Tarea</button>
        <div v-show="anadiendo" >
            <span v-on:click="noanade">x</span><br>
            <input type="text" v-model="descripcion" placeholder="Descripcion" @keyup.enter="agregar"><br>
            <button v-on:click="agregar">Anadir</button>
        </div>
    </div>
    <div class="contenedor_lista">
        <ul class="lista">
            <li v-bind:class="{tarea: !tar.completada, tareaCompletada:tar.completada}" v-for="tar, indice in tareas" v-bind:key="indice">
                <h3>{{tar.descripcion}}</h3>
                <p>{{tar.tiempo}} minutos</p>
                
                <input type="radio" name="prioridad " v-bind:id="'alta_' + indice" v-on:click="prioAlta(indice)"><label v-bind:for="'alta_' + indice">Alta</label>
                <input type="radio" name="prioridad " v-bind:id="'media' + indice" v-on:click="prioMedia(indice)"><label v-bind:for="'media' + indice">Media</label>
                <input type="radio" name="prioridad " v-bind:id="'baja' + indice" v-on:click="prioBaja(indice)"><label v-bind:for="'baja' + indice">Baja</label><br>
                <img src="fotos/completado.png" id="eliminar" class="hecho" v-on:click="completar(indice)">
                
                <img src="fotos/basura.png" id="eliminar" class="eliminar" v-on:click="eliminar(indice)">
            </li>
        </ul>
    </div>
    <footer>
        <button id="limpiar" class="derecha" v-on:click="limpiar"> Borrar Historial</button><br>
        <button id="limpiar" class="izquierda" v-on:click="eliminarCompletadas"> Eliminar Completadas</button>
        <p>Has completado {{this.contadorCompletadas}} tareas</p>
    </footer>
</div>
</template>

<script>
export default {
    data(){
        return {
            tareasCompletadas: [],
            tareasNoCompletadas: [],
            tareas: [],
            descripcion:'',
            anadiendo: false,
            contador:0,
            contadorCompletadas:0
        }
    },
    methods: {
        agregar(){
          var tarea={
                descripcion:this.descripcion,
                fecha:new Date(),
                prioridad:0,
                completada:false,
                tiempo: 0,
            };
          this.tareas.push(tarea);
          this.ordenar();
          this.contador++
          this.DoLocalStorage();
          this.descripcion='';
            
        },
        anade(){
            this.anadiendo= true;
        },
        noanade(){
            this.anadiendo= false;
        },
        DoLocalStorage(){
            localStorage.setItem('Tareas', JSON.stringify(this.tareas));
            localStorage.setItem('Contador', JSON.stringify(this.contador))
            localStorage.setItem('ContadorCompletadas', JSON.stringify(this.contadorCompletadas))
        },
        ordenar(){
            this.tareas.sort(this.comparar);
        },
        completar(indice){
            this.tareas[indice].completada =  !this.tareas[indice].completada;
            this.contadorCompletadas++

            this.DoLocalStorage();
        },
        eliminar(indice){
            this.tareas.splice(indice,1);
            this.ordenar();
            this.DoLocalStorage();
            
        },
        limpiar(){
            this.tareas = [];
            this.contador = 0;
            this.contadorCompletadas = 0;

            this.ordenar();
            this.DoLocalStorage();
            
        },
        eliminarCompletadas(){
            this.tareas = this.tareas.filter(this.completada)

            this.ordenar();
            this.DoLocalStorage();
        },
        prioAlta(indice){
            this.tareas[indice].prioridad = 2;

            this.ordenar();
            this.DoLocalStorage();
        },
        prioBaja(indice){
            this.tareas[indice].prioridad = 0;

            this.ordenar();
            this.DoLocalStorage();
        },
        prioMedia(indice){
            this.tareas[indice].prioridad = 1;

            this.ordenar();
            this.DoLocalStorage();
        },
        comparar(a,b) {
          return (b.prioridad-a.prioridad)
        },
        completada(tarea){
          return !tarea.completada
        },
    },
    mounted(){
        if(localStorage.getItem('Tareas')){
            this.tareas = JSON.parse(localStorage.getItem('Tareas'))
            this.ordenar();
        }
        if(localStorage.getItem('Contador')){
            this.contador = JSON.parse(localStorage.getItem('Contador'))
        }
        if(localStorage.getItem('ContadorCompletadas')){
            this.contadorCompletadas = JSON.parse(localStorage.getItem('ContadorCompletadas'))//lo pongo asi porque quier que se guarde una especie de historial de todas las que se han completado, no solo las actuales
        }
    },
}
</script>

<style>
*{
    padding: 0;
    margin: 0;
}

html{
    overflow: hidden;
}

ul, li{
    list-style: none;
}

header{
    height: 140px;
}

header img{
    opacity: 90%;
    height: 250px;
    width: 100%;
}

header h1{
    font-weight: bold;
    position: fixed;
    top: 60px;
    margin-left: 40%;
    background-image: linear-gradient(to left, violet, indigo, green, blue, yellow, orange, red);
    -webkit-background-clip: text;
    color: transparent;    
    font-size: 50pt;
}

body{
    background: linear-gradient(to right, orange, #ec38bc, #7303c0, cyan); 
}

section{
    text-align: center;
    padding-top: 10px;
    margin: auto;
}

button{
    padding: 5px;
    border: none;
    background-color: white;
    cursor: pointer;
    border-radius: 5px;
    margin-bottom: 10px;
    box-shadow: purple 1px 5px 10px;
}

.anadir_tarea{
    font-size: 20pt;
}

.anadir{
    border-radius: 2px;
    z-index: 100;
    position: relative;
    padding: 10px;
    margin: auto;
    background-color: white;
    margin-bottom: 1rem;
    width: 300px;
    box-shadow: black 1px 5px 10px;
}

.contenedor_lista{
    z-index: 100;
    position: relative;
    height: 200px;
    padding: 10px;
    margin: auto;
    margin-bottom: 1rem;
    width: 300px;
    box-shadow: black 1px 5px 10px;
    overflow: auto;
    background-color: white;
    border-radius: 2px;
} 

.contenedor_lista::-webkit-scrollbar {
    width: 15px;
}

.lista{
    padding-top: 10px;
    padding-bottom: 10px;
}

.anadir span{
    padding-top: 5px;
    padding-right: 0.5rem;
    float: right;
    font-size: 20pt;
    cursor: pointer;
}

input{
    margin: 10px
}
.eliminar{
    padding-top: 5px;
    padding-right: 0.5rem;
    cursor: pointer;
    width: 20px;
    
}
.hecho{
    padding-top: 5px;
    padding-right: 0.5rem;
    cursor: pointer;
    width: 30px;
    
}
.tarea{
    margin-bottom: 30px;
    box-shadow: red 1px 5px 10px;
    color: red;
}
.tarea span,
.tareaCompletada span{
    padding-top: 5px;
    padding-right: 0.5rem;
    float: right;
    font-size: 20pt;
    cursor: pointer;
}
.tareaCompletada{
    margin-bottom: 30px;
    box-shadow: green 1px 5px 10px;;
    color: green;
    text-decoration: line-through;
}


footer{
    height: 100px;
    padding: 10px;
    margin: auto;
    width: 300px;
    box-shadow: black 1px 5px 10px;
    overflow: auto;
    background-color: white;
    border-radius: 2px;
}
</style>
