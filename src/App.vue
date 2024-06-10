<script setup>
import {ref, reactive, watch} from 'vue';
import Presupuesto from './components/Presupuesto.vue';
import ControlPresupuesto from './components/ControlPresupuesto.vue';
import Gasto from './components/Gasto.vue';
import Modal from './components/Modal.vue';
import Filtros from './components/Filtros.vue'
import {generarId} from './helpers';
import iconoNuevoGasto from './assets/img/nuevo-gasto.svg'

const modal = reactive({
  mostrar:false,
  animar: false
})

const presupuesto = ref(0)
const disponible = ref(0)
const gastado = ref(0);
const filtro = ref('');

const gasto = reactive({
  nombre: '',
  cantidad: '',
  categoria: '',
  id: '',
  fecha: Date.now()
})

const gastos = ref([])

watch(gastos, ()=>{
  const totalGastado = gastos.value.reduce((total, gasto) => gasto.cantidad + total, 0)
  gastado.value = totalGastado;
  disponible.value = presupuesto.value - gastado.value
}, {
  deep: true//gastos es un arreglo que tiene muchos objetos
})

// la forma de fernando de la forma de resatear el modal en blanco
watch(modal, ()=>{
  if (!modal.mostrar) {
    reiniciaStateGasto();
  }
}, {
  deep: true
})

const definirPresupuesto = (cantidad) => {
  presupuesto.value = cantidad
  disponible.value = cantidad //
}

const mostrarModal = () =>{
  modal.mostrar = true
  setTimeout(()=>{
    modal.animar = true
  }, 300)
}

const ocultarModal = () =>{
  modal.animar = false
  setTimeout(()=>{
    // mi forma de resatear el modal en blanco
    // reiniciaStateGasto()
    // Object.assign(gasto, {
    //   nombre: '',
    //   cantidad: '',
    //   categoria: '',
    //   id: null,
    //   fecha: Date.now()
    // })
    modal.mostrar = false
  }, 300)
}

const guardarGasto = () => {
    if (gasto.id) {
      // Editando
      const { id } = gasto;
      const i = gastos.value.findIndex((gasto => gasto.id === id))
      gastos.value[i] = {...gasto}
    }else{
      // Registro nuevo
      gastos.value.push({
        ...gasto,
        id: generarId()
      })
    }
    
    ocultarModal()

    // Object.assign(gasto, {
    //   nombre: '',
    //   cantidad: '',
    //   categoria: '',
    //   id: null,
    //   fecha: Date.now()
    // })
    reiniciaStateGasto()
    console.log('gasto', gasto);
    console.log('gastos', gastos);
}

const seleccionarGasto = id =>{
  console.log('id actualizar', id);
  const gastosEditar = gastos.value.filter(gasto => gasto.id === id)[0];
  Object.assign(gasto, gastosEditar);
  mostrarModal();
}

const eliminarGasto = () =>{
  if (confirm('Eliminar?')) {
    gastos.value = gastos.value.filter(gastoStage => gastoStage.id !== gasto.id)
    ocultarModal()
  }
}

const reiniciaStateGasto = ()=>{
  Object.assign(gasto, {
      nombre: '',
      cantidad: '',
      categoria: '',
      id: null,
      fecha: Date.now()
    })
}
</script>

<template>
  <div 
    :class="{fijar: modal.mostrar}"
  >
    <header>
      <h1>
        Planificador de Gastos
      </h1>
      <div class="contenedor-header contenedor sombra">
        <Presupuesto 
          v-if="presupuesto === 0"
          @definir-presupuesto="definirPresupuesto"
        />
        <ControlPresupuesto 
          v-else
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
        />
      </div>
    </header>

   

    <Filtro />
  </div>


  <main v-if="presupuesto > 0 ">
      <Filtros
        v-model:filtro="filtro"
      />
      <div class="listado-gastos contenedor">
        <h2>{{ gastos.length > 0 ? 'Gastos' : 'No hay gastos' }}</h2>

        <Gasto 
          v-for="gasto in gastos"
          :key="gasto.id"
          :gasto="gasto"
          @seleccionar-gasto="seleccionarGasto"
        />
      </div>

      <div class="crear-gasto">
        <img
          :src="iconoNuevoGasto"
          alt="icono nuevo gasto"
          @click="mostrarModal"
        >
      </div>

      <Modal 
        v-if="modal.mostrar"
        @ocultar-modal="ocultarModal"
        @guardar-gasto="guardarGasto"
        @eliminar-gasto="eliminarGasto"
        :modal="modal"
        :disponible="disponible"
        :id="gasto.id"
        v-model:nombre = "gasto.nombre"
        v-model:cantidad = "gasto.cantidad"
        v-model:categoria = "gasto.categoria"
      />
    </main>

</template>

<style>
 :root {
  --azul: #3b82f6;
  --blanco: #fff;
  --gris-claro: #f5f5f5;
  --gris: #94a3b8;
  --gris-oculo: #64748b;
  --negro: #000
 }

 html{
  font-size: 62.5%;
  box-sizing: border-box;
 }
/* Resect */
 *,
 *:before,
 *:after{
  box-sizing: inherit;
 }

 body{
  font-size: 1.6rem;
  font-family: "Lato", sans-serif;
  background-color: var(--gris-claro);
 }

 h1{
  font-size: 4rem;
 }

 h2{
  font-size: 3rem;
 }

 .fijar{
  overflow: hidden;
  height: 100vh;
 }

 header{
  background-color: var(--azul);
 }

 header h1{
  padding: 3rem 0;
  margin: 0;
  color: var(--blanco);
  text-align: center;
 }

 .contenedor{
  width: 90%;
  max-width: 80rem;
  margin: 0 auto;
 }

 .contenedor-header{
  margin-top: -5rem;
  transform: translateY(5rem);
  padding: 5rem;
 }

 .sombra{
  -webkit-box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  -moz-box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  background-color: var(--blanco);
  border-radius: 1.2rem;
  padding: 5rem;
 }

 .crear-gasto{
  position: fixed;
  bottom: 5rem;
  right: 5rem;
 }

 .crear-gasto img{
    width: 5rem;
    cursor: pointer;
 }

 .listado-gastos{
    margin-top: 10rem;
 }

 .listado-gastos h2{
    font-weight: 700;
    color: var(--gris-oculo);
 }

</style>
