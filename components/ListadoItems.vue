<template>
    <div :class="rootClass">
        <b-card-group columns>
            <b-card v-for="(dataItem, index) in dataItemsLocal" :key="index" img-top :img-src="dataItem.src"
                :img-alt="dataItem.alt" :title="dataItem.alt">
                <b-card-text>
                    <p>Tipo: {{dataItem.tipo}}</p>
                    <p>Precio: {{dataItem.price}}</p>
                </b-card-text>
                <template #footer>
                    <small class="text-muted">Last updated 3 mins ago</small>
                </template>
            </b-card>
        </b-card-group>
    </div>
</template>

<script>
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
    }
};
</script>