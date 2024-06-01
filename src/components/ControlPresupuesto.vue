<script setup>
    import imagen from "../assets/img/grafico.jpg";
    import {formatearCantidad} from '../helpers'

    const props = defineProps({
        presupuesto:{
            type: Number, 
            required: true
        },
        disponible:{
            type: Number, 
            required: true
        }
    })
</script>


<template>
    <div class="dos-columnas">
        <div class="contenedor-grafico">
            <img 
                :src="imagen" alt="prueba"
            >
        </div>

        <div class="contenedor-presupuesto">
            <button class="reset-app">
                Resetear App
            </button>

            <p>
                <span>
                    Presupuesto:
                </span>
                 {{ formatearCantidad(presupuesto) }}
            </p>

            <p>
                <span>
                    Disponible:
                </span>
                 {{ formatearCantidad(disponible) }}
            </p>

            <p>
                <span>
                    Gastado:
                </span>
                $0
            </p>
        </div>

    </div>
</template>


<style scoped>
    .dos-columnas{
       display: flex;
       flex-direction: column; 
    }

    .dos-columnas > :first-child{
        margin-bottom: 4rem;
    }

    @media (min-width: 768px){
        .dos-columnas{
                flex-direction: row; 
                gap: 4rem;
                align-items: center;
            }

        .dos-columnas > :first-child{
             margin-bottom: 0;
        }
    }

    .reset-app{
        background-color: #DB2777;
        border: none;
        padding: 1rem;
        width: 100%;
        color: var(--blanco);
        border-radius: 10px;
        font-weight: 900;
        text-transform: uppercase;
        transition-property: background-color;
        transition-duration: 300ms;
    }

    .reset-app:hover{
        background-color: #7b0239;
        cursor: pointer;
        color: var(--blanco);
    }

    .contenedor-presupuesto{
        width: 100%;
    }

    .contenedor-presupuesto p{
        font-size: 2.4rem;
        text-align: center;
        color:rgb(69, 69, 69);
    }

    @media (min-width: 768px){
        .contenedor-presupuesto p{
            text-align: left;
        }
    }

    .contenedor-presupuesto span{
        font-weight: 900;
        color: var(--azul);
    }
</style>