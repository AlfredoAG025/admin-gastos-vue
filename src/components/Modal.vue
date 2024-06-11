<script setup>
import { ref, computed } from 'vue';
import Alerta from './Alerta.vue';

import cerrarModal from '../assets/img/cerrar.svg';

const error = ref('');

const emit = defineEmits(['ocultar-modal', 'guardar-gasto', 'eliminar-gasto', 'update:nombre', 'update:cantidad', 'update:categoria']);

const props = defineProps({
    modal: {
        type: Object,
        required: true,
    },
    nombre: {
        type: String,
        required: true,
    },
    cantidad: {
        type: [String, Number],
        required: true,
    },
    categoria: {
        type: String,
        required: true,
    },
    disponible: {
        type: Number,
        required: true,
    },
    id: {
        type: [String, null],
        required: true,
    },
});

const old = props.cantidad;

const agregarGasto = () => {
    // Validar que no haya campos vacíos

    const { nombre, cantidad, categoria, disponible, id } = props;

    if ([nombre, cantidad, categoria].includes('')) {
        error.value = 'Todos los campos son obligatorios';

        setTimeout(() => {
            error.value = ''
        }, 3000);

        return;
    }

    // Validar la cantidad

    if (cantidad <= 0) {
        error.value = 'Cantidad no válida';

        setTimeout(() => {
            error.value = ''
        }, 3000);
        return;
    }

    if (id) {
        if (cantidad > old + disponible) {
            error.value = 'Has excedido el presupuesto';

            setTimeout(() => {
                error.value = ''
            }, 3000);
            return;
        }
    } else {
        if (cantidad > disponible) {
            error.value = 'Has excedido el presupuesto';

            setTimeout(() => {
                error.value = ''
            }, 3000);
            return;
        }
    }

    emit('guardar-gasto');
}

const isEditing = computed(() => {
    return props.id
});
</script>


<template>
    <div class="modal">
        <div class="cerrar-modal">
            <img :src="cerrarModal" @click="$emit('ocultar-modal')">
        </div>

        <div class="contenedor contenedor-formulario" :class="[modal.animar ? 'animar' : 'cerrar']">
            <form @submit.prevent="agregarGasto" class="nuevo-gasto">
                <legend>{{ !isEditing ? 'Añadir Gasto' : 'Guardar Cambios' }}</legend>

                <Alerta v-if="error">
                    {{ error }}
                </Alerta>

                <div class="campo">
                    <label for="nombre">Nombre Gasto:</label>
                    <input autofocus type="text" name="nombre" id="nombre" placeholder="Añade el nombre del Gasto"
                        :value="nombre" @input="$emit('update:nombre', $event.target.value)">
                </div>

                <div class="campo">
                    <label for="cantidad">Cantidad:</label>
                    <input type="number" name="cantidad" id="cantidad"
                        placeholder="Añade la cantidad del Gasto, ej. 300" :value="cantidad"
                        @input="$emit('update:cantidad', +$event.target.value)">
                </div>

                <div class="campo">
                    <label for="categoria">Categoría:</label>
                    <select name="categoria" id="categoria" :value="categoria"
                        @input="$emit('update:categoria', $event.target.value)">
                        <option value="">-- Seleccione --</option>
                        <option value="ahorro">Ahorro</option>
                        <option value="comida">Comida</option>
                        <option value="casa">Casa</option>
                        <option value="gastos">Gastos Varios</option>
                        <option value="ocio">Ocio</option>
                        <option value="salud">Salud</option>
                        <option value="subscripciones">Subscripciones</option>
                    </select>
                </div>

                <input type="submit" :value="[!isEditing ? 'Añadir Gasto' : 'Guardar Cambios']">
            </form>

            <button v-if="isEditing" 
            @click="$emit('eliminar-gasto', id)"
            type="button" class="btn-eliminar">
                Eliminar Gasto
            </button>
        </div>
    </div>
</template>

<style scoped>
.cerrar-modal {
    position: absolute;
    right: 3rem;
    top: 3rem;
}

.cerrar-modal img {
    width: 3rem;
    cursor: pointer;
}

.contenedor-formulario {
    transition-property: all;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
    opacity: 0;
}

.contenedor-formulario.animar {
    opacity: 1;
}

.contenedor-formulario.cerrar {
    opacity: 0;
}

.modal {
    position: absolute;
    background-color: rgb(0 0 0 / 0.9);
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
}

.nuevo-gasto {
    margin: 10rem auto 0 auto;
    display: grid;
    gap: 2rem;
}

.nuevo-gasto legend {
    text-align: center;
    color: var(--blanco);
    font-size: 3rem;
    font-weight: 700;
}

.campo {
    display: grid;
    gap: 2rem;
}

.nuevo-gasto input,
.nuevo-gasto select {
    background-color: var(--gris-claro);
    border-radius: 1rem;
    padding: 1rem;
    border: none;
    font-size: 2.2rem;
}

.nuevo-gasto label {
    color: var(--blanco);
    font-size: 3rem;
}

.nuevo-gasto input[type="submit"] {
    background-color: var(--azul);
    color: var(--blanco);
    font-weight: 700;
    cursor: pointer;
}

.btn-eliminar {
    border: none;
    padding: 1rem;
    width: 100%;
    background-color: #ef4444;
    font-weight: 700;
    font-size: 1.2rem;
    color: var(--blanco);
    margin-top: 10rem;
    cursor: pointer;
}
</style>