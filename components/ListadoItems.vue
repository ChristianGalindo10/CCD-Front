<template>
    <div :class="rootClass">
        <b-card-group columns>
            <b-card v-for="(dataItem, index) in dataItemsLocal" :key="index" img-top :img-src="dataItem.src"
                :img-alt="dataItem.alt" :title="dataItem.alt">
                <b-card-text>
                    <p>Restaurante: {{ dataItem.nombre_restaurante }}</p>
                    <p v-if="dataItem.tipo == 'Si'">Personalizable: {{ dataItem.tipo }}</p>
                    <p v-else-if="dataItem.tipo == 'No'">Personalizable: {{ dataItem.tipo }}</p>
                    <p v-else>Tipo: {{ dataItem.tipo }}</p>
                    <p>Precio: {{ dataItem.price }}</p>
                </b-card-text>
                <template #footer>
                    <div :class="{ centrado: userLoged }">
                        <a href="#" v-if="userLoged"><span>Añadir al carrito</span>
                            <b-icon-cart-plus></b-icon-cart-plus>
                        </a>
                        <small class="text-muted" v-else>Last updated 3 mins ago</small>
                    </div>

                </template>
            </b-card>
        </b-card-group>
    </div>
</template>
<style scoped>
.centrado {
    text-align: center;
}
</style>
<script>
import { BIcon, BIconCartPlus } from 'bootstrap-vue'
export default {
    name: "listado-items",
    props: {
        dataItems: {
            type: Array,
            default: () => []
        },
        rootClass: {
            type: String,
            default: "vue-select-image"
        },
        userLoged: {
            type: Boolean,
            default: false
        }
    },
    computed: {
        dataItemsLocal: function () {
            return this.dataItems || [];
        }
    },
    mounted() {
        // set initial 
    },
    methods: {
    },
    components: { BIcon, BIconCartPlus }
};
</script>